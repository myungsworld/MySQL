# MySQL

- Err
    - localhost:3306: Address already in use -> 
        - kill $(lsof -t -i:3306)
    
    - Error 1215: Cannot add foreign key constraint
        - Teacher *Teacher `json:"teacher" gorm:"foreignKey:Id;constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`
        - -> 
        - TeacherID int `json:"teacher_id"
        - Teacher *Teacher `json:"teacher" gorm:"constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`
- Grammer
    -[외래키](http://www.tcpschool.com/mysql/mysql_constraint_foreignKey)

- 
