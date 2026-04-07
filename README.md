## Game Module (In Progress)

### Target Users

- **Students**: Ages 12-15
- **Admin**: Game management and analytics


### Database Schema

#### Enums

```prisma
enum GameType {
  MATH
  LANGUAGE
  SCIENCE
  QUIZ
  GENERAL
}

enum Difficulty {
  EASY
  MEDIUM
  HARD
}

enum MissionType {
  LESSON
  QUIZ
  CHALLENGE
  EXAM
}

enum QuestionType {
  MULTIPLE_CHOICE
  TRUE_FALSE
  SHORT_ANSWER
}

enum ProgressStatus {
  NOT_STARTED
  IN_PROGRESS
  COMPLETED
}

enum LeaderboardPeriod {
  DAILY
  WEEKLY
  MONTHLY
  ALL_TIME
}
```

#### Tables

| Table               | Purpose                              |
| ------------------- | ------------------------------------ |
| `Game`              | Educational games (Math, Quiz, etc.) |
| `Category`          | Topics within games                  |
| `Mission`           | Levels/challenges with XP rewards    |
| `Question`          | Quiz questions                       |
| `PlayerProgress`    | Student progress tracking            |
| `Achievement`       | Achievement definitions              |
| `PlayerAchievement` | Earned achievements                  |
| `Leaderboard`       | Rankings (daily/weekly/all-time)     |

#### Table Columns

**Game**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| name | String | Game name |
| description | String? | Game description |
| type | GameType | MATH, LANGUAGE, SCIENCE, QUIZ, GENERAL |
| difficulty | Difficulty | EASY, MEDIUM, HARD (default: EASY) |
| icon | String? | Game icon URL |
| coverImage | String? | Cover image URL |
| isActive | Boolean | Active status (default: true) |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |

**Category**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| gameId | String | Foreign key to Game |
| name | String | Category name |
| description | String? | Category description |
| icon | String? | Category icon |
| order | Int | Display order |
| isActive | Boolean | Active status |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |

**Mission**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| categoryId | String | Foreign key to Category |
| gameId | String | Foreign key to Game |
| title | String | Mission title |
| description | String? | Mission description |
| type | MissionType | LESSON, QUIZ, CHALLENGE, EXAM |
| order | Int | Display order |
| xpReward | Int | XP earned on completion |
| timeLimit | Int? | Time limit in minutes (null = no limit) |
| minScore | Int? | Minimum score to pass (%) |
| isActive | Boolean | Active status |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |

**Question**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| missionId | String | Foreign key to Mission |
| question | String | Question text |
| type | QuestionType | MULTIPLE_CHOICE, TRUE_FALSE, SHORT_ANSWER |
| options | Json? | Array of options for MULTIPLE_CHOICE |
| answer | String | Correct answer |
| explanation | String? | Explanation after answering |
| order | Int | Display order |
| points | Int | Points for this question |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |

**PlayerProgress**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| userId | String | Foreign key to User |
| missionId | String | Foreign key to Mission |
| status | ProgressStatus | NOT_STARTED, IN_PROGRESS, COMPLETED |
| score | Int? | Score achieved (%) |
| correctCount | Int? | Number of correct answers |
| totalQuestions | Int? | Total questions in mission |
| attempts | Int | Number of attempts |
| startedAt | DateTime? | When mission was started |
| completedAt | DateTime? | When mission was completed |
| timeSpent | Int? | Time spent in seconds |
| answers | Json? | User's answers { questionId: answer } |
| createdAt | DateTime | Creation timestamp |
| updatedAt | DateTime | Last update timestamp |

**Achievement**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| name | String | Achievement name |
| description | String? | Achievement description |
| icon | String? | Achievement icon |
| category | String? | Category (Math, Speed, Consistency) |
| xpReward | Int | XP reward |
| condition | Json? | Condition { type, value } |
| isActive | Boolean | Active status |
| createdAt | DateTime | Creation timestamp |

**PlayerAchievement**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| userId | String | Foreign key to User |
| achievementId | String | Foreign key to Achievement |
| earnedAt | DateTime | When achievement was earned |

**Leaderboard**
| Column | Type | Description |
|--------|------|-------------|
| id | String (UUID) | Primary key |
| gameId | String | Foreign key to Game |
| period | LeaderboardPeriod | DAILY, WEEKLY, MONTHLY, ALL_TIME |
| rank | Int | Rank position |
| userId | String | Foreign key to User |
| totalXP | Int | Total XP |
| totalScore | Int | Total score |
| missionsCompleted | Int | Number of missions completed |
| updatedAt | DateTime | Last update timestamp |
| createdAt | DateTime | Creation timestamp |

### Module Structure

```
src/modules/game/
├── game.module.ts
├── game.controller.ts         (Admin CRUD)
├── game.service.ts
├── player/
│   ├── player.controller.ts   (Player endpoints)
│   └── player.service.ts
├── dto/
│   ├── create-game.dto.ts
│   ├── update-game.dto.ts
│   ├── create-mission.dto.ts
│   └── index.ts
```

### API Endpoints (Planned)

#### Admin Endpoints

```
POST   /admin/games                    - Create game
GET    /admin/games                    - List all games
GET    /admin/games/:id                - Get game details
PATCH  /admin/games/:id                - Update game
DELETE /admin/games/:id                - Delete game

POST   /admin/games/:gameId/categories - Create category
PATCH  /admin/categories/:id           - Update category
DELETE /admin/categories/:id           - Delete category

POST   /admin/categories/:categoryId/missions - Create mission
PATCH  /admin/missions/:id              - Update mission
DELETE /admin/missions/:id              - Delete mission

POST   /admin/missions/:missionId/questions - Create question
PATCH  /admin/questions/:id             - Update question
DELETE /admin/questions/:id             - Delete question

GET    /admin/achievements              - List achievements
POST   /admin/achievements              - Create achievement
PATCH  /admin/achievements/:id          - Update achievement
DELETE /admin/achievements/:id         - Delete achievement

GET    /admin/analytics                 - View analytics
```

#### Player Endpoints

```
GET    /games                           - List available games
GET    /games/:id                       - Get game details
GET    /games/:id/categories            - Get game categories
GET    /games/:id/missions               - Get game missions
GET    /missions/:id/questions          - Get mission questions

POST   /player/progress/:missionId/start    - Start mission
POST   /player/progress/:missionId/complete  - Complete mission

GET    /player/progress                 - Get my progress
GET    /player/progress/:missionId      - Get mission progress

GET    /player/achievements             - Get my achievements
GET    /leaderboard/:gameId              - Get leaderboard
GET    /leaderboard/:gameId?period=     - Get leaderboard (daily/weekly/monthly/all_time)
```
