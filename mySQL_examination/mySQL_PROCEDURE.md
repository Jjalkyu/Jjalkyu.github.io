## 예제 1: 특정 팀의 승리 여부에 따라 메시지를 반환하는 프로시저
- 팀의 경기 결과에 따라 "승리", "패배", "무승부" 메시지를 반환하는 프로시저입니다.

- ~~ 에 적당한 소스 넣기
 
 ```
DELIMITER //
drop procedure CheckTeamMatchResult;
CREATE PROCEDURE CheckTeamMatchResult(IN matchID INT, IN teamID INT, OUT resultMessage VARCHAR(50))
BEGIN
   DECLARE teamA INT;
   DECLARE teamB INT;
   DECLARE result VARCHAR(10);

   SELECT team_a, team_b, result INTO teamA, teamB, result FROM Matches WHERE match_id = matchID;

   IF teamA = teamID AND result = 'Win' THEN
       SET ~~ = 'Team won the match';
   ELSEIF teamB = teamID AND result = 'Lose' THEN
       SET ~~ = 'Team won the match';
   ELSEIF teamA = teamID AND result = 'Lose' THEN
       SET ~~ = 'Team lost the match';
   ELSEIF teamB = teamID AND result = 'Win' THEN
       SET ~~ = 'Team lost the match';
   ELSE
       SET ~~ = 'Match ended in a draw';
   END IF;
END //

call CheckTeamMatchResult();

DELIMITER ;
select * from Matches;
call CheckTeamMatchResult(1, 1, @myValue);
SELECT CONCAT(@myValue);
```

## 문제풀이
- elif 문을 사용한다.

```
DROP PROCEDURE IF EXISTS CheckTeamMatchResult;
DELIMITER //
CREATE PROCEDURE CheckTeamMatchResult(IN matchID INT, IN teamID INT, OUT resultMessage VARCHAR(50))
BEGIN
  DECLARE teamA INT;
  DECLARE teamB INT;
  DECLARE result VARCHAR(10);

  SELECT team_a, team_b, result INTO teamA, teamB, result FROM Matches WHERE match_id = matchID;

  IF teamA = teamID AND result = 'Win' THEN
      SET resultMessage = 'Team won the match';
  ELSEIF teamB = teamID AND result = 'Lose' THEN
      SET resultMessage = 'Team won the match';
  ELSEIF teamA = teamID AND result = 'Lose' THEN
      SET resultMessage = 'Team lost the match';
  ELSEIF teamB = teamID AND result = 'Win' THEN
      SET resultMessage = 'Team lost the match';
  ELSE
      SET resultMessage = 'Match ended in a draw';
  END IF;
END //
DELIMITER ;
call CheckTeamMatchResult(2, 1, @myValue);
SELECT @myValue;
```

## 예제 2: 리그의 팀 평균 점수에 따라 랭킹을 반환하는 프로시저
- 리그의 팀 평균 점수가 특정 기준 이상인지 여부에 따라 리그가 "상위 리그", "중간 리그", "하위 리그"로 평가됩니다.
- averageScore 가 3.0 이상인것 Top League
- averageScore 가 2.0이상 2.9이하 Mid League
- 나머지 Low League
- SET ranking = 'Top League';
- SET ranking = 'Mid  League';
- SET ranking = 'Low League';

```
DELIMITER //
CREATE PROCEDURE LeagueRanking(IN leagueID INT, OUT ranking VARCHAR(20))
BEGIN
   DECLARE averageScore DECIMAL(5,2);

   SELECT AVG(CAST(SUBSTRING_INDEX(score, '-', 1) AS DECIMAL)) INTO averageScore
   FROM Matches
   WHERE league_id = leagueID;
   if 
		~~~
        
END IF;
END //
DELIMITER ;

call LeagueRanking(2, @myValue);
SELECT CONCAT(@myValue);
```

## 문제풀이
- elif 문을 사용한다

```
SELECT AVG(CAST(SUBSTRING_INDEX(score, '-', 1) AS DECIMAL))
  FROM Matches
  WHERE league_id = 1;

select * from matches;

DROP PROCEDURE IF EXISTS LeagueRanking;
DELIMITER //
CREATE PROCEDURE LeagueRanking(IN leagueID INT, OUT ranking VARCHAR(20))
BEGIN
  DECLARE averageScore DECIMAL(5,2);

  SELECT AVG(CAST(SUBSTRING_INDEX(score, '-', 1) AS DECIMAL)) INTO averageScore
  FROM Matches
  WHERE league_id = leagueID;
  if averageScore >= 3.0 THEN
       SET ranking = 'Top League';
  elseif averageScore BETWEEN 2.0 AND 2.9 THEN
      SET ranking = 'Mid  League';
  else
       SET ranking = 'Low League';
  end if;
  
END //
DELIMITER ;
call LeagueRanking(1, @myValue);
SELECT CONCAT(@myValue);
```
## 예제 3: 리그에 따라 각 팀의 평균 점수를 계산하는 프로시저

- ~~~에 소스를 넣어 완성하시오.

```
DELIMITER //

CREATE PROCEDURE GetAverageScoreByLeague(in league_id int , OUT averageScore DECIMAL(5,2))
BEGIN
   SELECT 
       AVG(   ~~~    CAST(SUBSTRING_INDEX(score, '-', 1) AS DECIMAL) 
                ELSE NULL END) AS averageScore
   INTO averageScore
   FROM Matches;
END //

DELIMITER ;
CALL GetAverageScoreByLeague(1, @myValue);
SELECT CONCAT(@myValue) '평균 점수';

```

## 문제풀이

```
DROP PROCEDURE IF EXISTS GetAverageScoreByLeague;
DELIMITER //
CREATE PROCEDURE GetAverageScoreByLeague( IN leagueID INT , OUT averageScore DECIMAL(5,2))
BEGIN
  SELECT 
      AVG(CASE WHEN league_id = leagueID THEN CAST(SUBSTRING_INDEX(score, '-', 1) AS DECIMAL) 
               ELSE NULL END) AS averageScore
  INTO averageScore
  FROM Matches;
END //
DELIMITER ;
CALL GetAverageScoreByLeague(2, @myValue);
SELECT CONCAT(@myValue) '평균 점수';
```
## 예제 4: 시즌에 따라 승/패 여부에 따른 전체 경기 통계를 반환하는 프로시저
- 괄호(  ) 에 CASE WHEN 소스를 넣어 완성하시오.

```
DELIMITER //

CREATE PROCEDURE GetSeasonStatistics(IN seasonYear VARCHAR(10), OUT totalWins INT, OUT totalLosses INT)
BEGIN
   SELECT 
       SUM(  
				) AS totalWins,
       SUM(             ) AS totalLosses
   INTO totalWins, totalLosses
   FROM Matches
   WHERE season = seasonYear;
END //

DELIMITER ;
 

CALL GetSeasonStatistics('2024', @totalWins, @totalLosses);
SELECT @totalWins '승전 합계', @totalLosses '패전 합계';
```
## 문제풀이
```
select * from Matches;
DROP PROCEDURE IF EXISTS GetSeasonStatistics;
DELIMITER //
CREATE PROCEDURE GetSeasonStatistics(IN seasonYear VARCHAR(10), OUT totalWins INT, OUT totalLosses INT)
BEGIN
  SELECT 
      SUM(CASE WHEN result = 'Win' THEN 1 ELSE 0 END) AS totalWins,
      SUM(CASE WHEN result = 'Lose' THEN 1 ELSE 0 END) AS totalLosses
  INTO totalWins, totalLosses
  FROM Matches
  WHERE season = seasonYear;
END //
DELIMITER ;
CALL GetSeasonStatistics('2023', @totalWins, @totalLosses);
SELECT @totalWins '승전 합계', @totalLosses '패전 합계';
```


## 예제 5: 특정 팀의 모든 경기 정보 조회

```
DROP PROCEDURE IF EXISTS GetTeamMatches;
DELIMITER //
CREATE PROCEDURE GetTeamMatches(IN team INT, OUT match_count INT)
BEGIN
   -- 특정 팀이 포함된 경기 수를 계산합니다.
   SELECT COUNT(*) INTO match_count
   FROM Matches
   WHERE team_a = team OR team_b = team;

   -- 해당 팀이 포함된 경기 정보 출력
   SELECT *
   FROM Matches
   WHERE team_a = team OR team_b = team;
END //
DELIMITER ;
-- 5번 팀 경지 정보를 조회 하시오

CALL ~~~~
SELECT @match_count;
```

## 2. 특정 팀의 최근 5경기 기록 조회
```
DROP PROCEDURE IF EXISTS GetRecentMatches;
DELIMITER //
CREATE PROCEDURE GetRecentMatches(IN team INT)
BEGIN
   -- 특정 팀이 포함된 최근 5경기 정보를 날짜 기준으로 정렬하여 조회합니다.
   ~~~
   LIMIT 5;
END //
DELIMITER ;
CALL GetRecentMatches(5);

select * from Matches;
```