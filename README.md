# MySQL

- GORM
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

