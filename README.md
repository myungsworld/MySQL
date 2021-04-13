# MySQL

- GORM
    - Take 와 Find 의 차이 
        - Take : 현재 이 테이블의 record가 없으면 에러 반환
        - Find : 현재 이 테이블의 레코드가 없어도 에러를 반환하지 않고 빈 테이블을 반환 
    - Distinct 
        - 찾고자 하는 컬럼의 중복을 제거(?) 하는 옵션 
        - ex Distinct("name","phone_number").Find(&tables): name과 phone_number 에서 name이 같아도 phone_number의 값이 중복이 아니니 둘다 반환   
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


- [Index](https://zorba91.tistory.com/292) 
