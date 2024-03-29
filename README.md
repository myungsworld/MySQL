# MySQL

- GORM for Go Language
    - **Take** 와 **Find** 의 차이 
        - Take : 현재 이 테이블의 record가 없으면 에러 반환
        - Find : 현재 이 테이블의 레코드가 없어도 에러를 반환하지 않고 빈 테이블을 반환 
    - **Distinct** 
        - 찾고자 하는 컬럼의 중복을 제거(?) 하는 옵션 
        - ex) Distinct("name","phone_number").Find(&tables): name과 phone_number 에서 name이 같아도 phone_number의 값이 중복이 아니니 둘다 반환   
    - **Unscoped**
        - Soft Delete 같은 기능을 다루기 위한 기능 
        - **Soft Delete** 
            - 디비에서 실제로 삭제하진 않고 query 할때 안나오는 기능이라고 해야 되나
            - 조건 : table에 DeletedAt `json:"deleted_at"` 필드가 있고 Delete 실행하면 자동으로 Soft Delete 가 일어남  
        - 이 Soft Delete를 한 필드를 찾거나 완전 삭제하기 위해 Unscoped가 사용됨
        - ex) db.Unscoped().Where(" id = ? ", userId).Find()
        - ex) db.Unscoped().Delete(&tables)
    - **Expr** 
        - 디비 변경할때 추가하거나 빼서 저장하는 기능
        - ex) db.Update("name", gorm.Expr("name + ", "짱"))
    - [**SubQuery**](https://snowple.tistory.com/360)
        - 걍 gorm 쓰려고 하지말고 raw 로 짜는게 속편함  
        - 이거 ㄹㅇ임    

    - 오름차순 내림 차순
    
```go
    db.Order("age desc, name").Find(&users)
    // SELECT * FROM users ORDER BY age desc, name;

    // Multiple orders
    db.Order("age desc").Order("name").Find(&users)
    // SELECT * FROM users ORDER BY age desc, name;

    db.Clauses(clause.OrderBy{
      Expression: clause.Expr{SQL: "FIELD(id,?)", Vars: []interface{}{[]int{1, 2, 3}}, WithoutParentheses: true},
    }).Find(&User{})
    // SELECT * FROM users ORDER BY FIELD(id,1,2,3)
```

- **`Index`**
    - **`B-Tree Index`** ( 보통 우리가 말하는 Index ) 
        - 비슷한 형식의 데이터에서 원하는 데이터를 찾는경우 용이
        - 위도 , 경도 , 나이 등등 특정 범위 안에서 찾을때 많이 씀 ex) 위도 -90~ +90 , 나이 0~150  
    - **`Full Text Index`** ( 전문 검색 Index )
        - 제목 , 컨텐츠의 내용 처럼 유저가 쓰기 나름인 데이터를 토근 단위로 쪼개서 검색에 용이한 인덱스  
        - `Buit in parser` 또는 `Stop-word parser`
            - 공백 기준으로 나눔
        - `N-gram parser`
            - 크기 n만큼 데이터를 인덱스로 파싱해두었다가 사용함    
    - [Gorm Index](https://gorm.io/es_ES/docs/indexes.html)
    - **인덱스 조회** : SHOW INDEXES FROM [Table Name];
        - Collation : 기본적인 정렬 형태 A:오름차순, NULL: 정렬 구분 없음
        - Cardinality : 해당 인덱스 값의 Unique 값 수
        - Index Type : 인덱스 모드 ( BTREE , FULLTEXT , HASH , TREE )    
    - **인덱스 삭제** : 위에서 조회된 key_name 을 기반으로 삭제 
        - ALTER TABLE [TABLE NAME] DROP FOREIGN KEY [KEY_NAME];
    
    - **`FULLTEXT`인덱스를 이용한 쿼리**
        - show variables like '%ft_min%' 을 검색하면 INNO DB = 3 , MYSQL = 4 로 되어있는데 검색을 위한 디폴트 최소 글자수
        - `SELECT * FROM [TABLE NAME] WHERE MATCH([COLUMN NAME]) AGAINST("*머시기머시기*" IN BOOLEAN MODE)`
        - gorm -> Where(fmt.Sprintf("MATCH([COLUMN NAME]) AGAINST('*%s*' IN BOOLEAN MODE)", body.NAME))



- Err
    - localhost:3306: Address already in use -> 
        - kill $(lsof -t -i:3306)
    
    - Error 1215: Cannot add foreign key constraint
        - Teacher *Teacher `json:"teacher" gorm:"foreignKey:Id;constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`
        - -> 
        - TeacherID int `json:"teacher_id"
        - Teacher *Teacher `json:"teacher" gorm:"constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`
    - Eroor 1452:  Cannot add or update a child row: a foreign key constraint fails
        - 말 그대로 외래키 제약조건에 충족하지 못했다 이말이다
        
    - Error 1064: Check the manual that corresponds to your MySQL server version for the right syntax to use near 'OFFSET 2' at line 1
        - ->
        - you can't use offset without limit 
        - 그럼 공홈에 3번째 달락은 뭐고 
        - ![스크린샷 2021-04-13 오후 2 11 43](https://user-images.githubusercontent.com/56465854/114499986-2e277d00-9c62-11eb-914c-cfab1ebd80b1.png)


  

**MySQL 시간별 조회**. 
- 하루에 특정시간별 데이터 양 조회, ex)현재까지의 9시부터 19시 사이의 시간별 데이터 양 
```mysql
SELECT HOUR(시간컬럼) as HOUR , COUNT(시간컬럼) as COUNT
FROM 조회할 테이블
WHERE 시간컬럼 BETWEEN '0000-01-01 00:00:00' AND DATE_FORMAT(now(),'%Y-%m-%d 23:59:59')
GROUP BY HOUR HAVING HOUR BETWEEN 9 AND 19
ORDER BY HOUR ASC
```

- 하루에 특정시간별 데이터 양 조회 + 빈값도 출력 
```mysql
SET @hour := -1

SELECT (@hour := @hour + 1) as HOUR,(SELECT COUNT(*) FROM 조회할 테이블 WHERE 시간컬럼 = @hour ) as COUNT
FROM 조회할 테이블
WHERE @hour < 23
```
