# Soccer Team Management System

This project is a soccer team management system implemented using GraphQL. It provides functionality to manage teams, players, matches, transfers, and injury records.

## Table of Contents

1. [Requirements](#requirements)
2. [GraphQL API Specification](#graphql-api-specification)
3. [GraphQL Schema](#graphql-schema)
4. [Database Schema](#database-schema)

## Requirements

1. Team Management
   - View, create, update, and delete team information
   - View list of players belonging to a team

2. Player Management
   - View, register, update, and delete player information
   - View player transfer history
   - View player injury records

3. Match Management
   - View, create, and update match schedules
   - Update match results
   - Real-time match score updates

4. Transfer Management
   - View and create transfer information
   - Real-time notifications for new transfers

5. Injury Record Management
   - View, create, and update injury records
   - Real-time notifications for player injury status changes

## GraphQL API Specification

### Queries

- `team(id: ID!)`: Retrieve specific team information
- `teams`: Retrieve list of all teams
- `player(id: ID!)`: Retrieve specific player information
- `players(teamId: ID, nationality: String, position: String)`: Retrieve list of players (with filtering options)
- `match(id: ID!)`: Retrieve specific match information
- `matches(startDate: Date!, endDate: Date!)`: Retrieve list of matches within a specific period
- `transfer(id: ID!)`: Retrieve specific transfer information
- `transfers(playerId: ID)`: Retrieve transfer history for a specific player
- `injuryRecord(id: ID!)`: Retrieve specific injury record
- `injuryRecords(playerId: ID)`: Retrieve injury records for a specific player

### Mutations

- `registerPlayer(input: PlayerInput!)`: Register a new player
- `updatePlayer(id: ID!, input: PlayerInput!)`: Update player information
- `deletePlayer(id: ID!)`: Delete a player
- `createTeam(input: TeamInput!)`: Create a new team
- `updateTeam(id: ID!, input: TeamInput!)`: Update team information
- `deleteTeam(id: ID!)`: Delete a team
- `addTransfer(input: TransferInput!)`: Add a new transfer record
- `updateMatchResult(input: MatchResultInput!)`: Update match result
- `createMatch(input: MatchInput!)`: Create a new match schedule
- `addInjuryRecord(input: InjuryRecordInput!)`: Add a new injury record
- `updateInjuryRecord(id: ID!, input: InjuryRecordInput!)`: Update an injury record

### Subscriptions

- `matchScoreUpdate(matchId: ID!)`: Real-time match score updates
- `newTransfer`: Notifications for new transfers
- `playerInjuryUpdate(teamId: ID!)`: Notifications for injury status changes of players in a specific team

## GraphQL Schema
```
type Query {
  team(id: ID!): Team
  teams: [Team]
  player(id: ID!): Player
  players(teamId: ID, nationality: String, position: String): [Player]
  match(id: ID!): Match
  matches(startDate: Date!, endDate: Date!): [Match]
  transfer(id: ID!): Transfer
  transfers(playerId: ID): [Transfer]
  injuryRecord(id: ID!): InjuryRecord
  injuryRecords(playerId: ID): [InjuryRecord]
}

type Mutation {
  registerPlayer(input: PlayerInput!): Player
  updatePlayer(id: ID!, input: PlayerInput!): Player
  deletePlayer(id: ID!): Boolean
  
  createTeam(input: TeamInput!): Team
  updateTeam(id: ID!, input: TeamInput!): Team
  deleteTeam(id: ID!): Boolean
  
  addTransfer(input: TransferInput!): Transfer
  updateMatchResult(input: MatchResultInput!): Match
  createMatch(input: MatchInput!): Match
  
  addInjuryRecord(input: InjuryRecordInput!): InjuryRecord
  updateInjuryRecord(id: ID!, input: InjuryRecordInput!): InjuryRecord
}

type Subscription {
  matchScoreUpdate(matchId: ID!): Match
  newTransfer: Transfer
  playerInjuryUpdate(teamId: ID!): InjuryRecord
}

type Team {
  id: ID!
  name: String!
  country: String!
  foundedYear: Int!
  stadiumName: String
  players: [Player]
  upcomingMatches: [Match]
  transfers: [Transfer]
}

type Player {
  id: ID!
  name: String!
  position: String!
  birthDate: Date!
  nationality: String!
  currentTeam: Team
  transfers: [Transfer]
  injuryRecords: [InjuryRecord]
}

type Match {
  id: ID!
  date: Date!
  homeTeam: Team!
  awayTeam: Team!
  score: String
  status: MatchStatus!
}

type Transfer {
  id: ID!
  player: Player!
  fromTeam: Team
  toTeam: Team!
  transferDate: Date!
  fee: Float
}

type InjuryRecord {
  id: ID!
  player: Player!
  injuryType: String!
  startDate: Date!
  endDate: Date
  status: InjuryStatus!
}

enum MatchStatus {
  SCHEDULED
  IN_PROGRESS
  COMPLETED
  CANCELLED
}

enum InjuryStatus {
  ACTIVE
  RECOVERED
}

input PlayerInput {
  name: String!
  position: String!
  birthDate: Date!
  nationality: String!
  teamId: ID!
}

input TeamInput {
  name: String!
  country: String!
  foundedYear: Int!
  stadiumName: String
}

input TransferInput {
  playerId: ID!
  fromTeamId: ID
  toTeamId: ID!
  transferDate: Date!
  fee: Float
}

input MatchInput {
  date: Date!
  homeTeamId: ID!
  awayTeamId: ID!
}

input MatchResultInput {
  matchId: ID!
  score: String!
  status: MatchStatus!
}

input InjuryRecordInput {
  playerId: ID!
  injuryType: String!
  startDate: Date!
  endDate: Date
  status: InjuryStatus!
}

scalar Date
```
## Database Schema

1. Teams Table
   - id: INT (Primary Key, Auto Increment)
   - name: VARCHAR(100)
   - country: VARCHAR(50)
   - founded_year: INT
   - stadium_name: VARCHAR(100)

2. Players Table
   - id: INT (Primary Key, Auto Increment)
   - name: VARCHAR(100)
   - position: VARCHAR(50)
   - birth_date: DATE
   - nationality: VARCHAR(50)
   - current_team_id: INT (Foreign Key referencing Teams.id)

3. Matches Table
   - id: INT (Primary Key, Auto Increment)
   - date: DATETIME
   - home_team_id: INT (Foreign Key referencing Teams.id)
   - away_team_id: INT (Foreign Key referencing Teams.id)
   - score: VARCHAR(10)
   - status: ENUM('SCHEDULED', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED')

4. Transfers Table
   - id: INT (Primary Key, Auto Increment)
   - player_id: INT (Foreign Key referencing Players.id)
   - from_team_id: INT (Foreign Key referencing Teams.id)
   - to_team_id: INT (Foreign Key referencing Teams.id)
   - transfer_date: DATE
   - fee: DECIMAL(10, 2)

5. InjuryRecords Table
   - id: INT (Primary Key, Auto Increment)
   - player_id: INT (Foreign Key referencing Players.id)
   - injury_type: VARCHAR(100)
   - start_date: DATE
   - end_date: DATE
   - status: ENUM('ACTIVE', 'RECOVERED')
