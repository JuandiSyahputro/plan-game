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
