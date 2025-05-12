# MongoDB

1. Geben Sie alle Informationen zu den Filialen aus.
db.departments.find()
2. db.emps.find(
  { deptno: 10 },
  { _id: 0, ename: 1, job: 1, hiredate: 1 }
)
3. db.emps.find(
  { job: "CLERK" },
  { _id: 0, ename: 1, job: 1, sal: 1 }
)
4. db.emps.find(
  { deptno: { $ne: 10 } }
)
5. db.emps.find(
  { $expr: { $gt: ["$comm", "$sal"] } }
)
6. db.emps.find(
  { hiredate: ISODate("1981-12-03T00:00:00Z") }
)
7. db.emps.find(
  {
    $or: [
      { sal: { $lt: 1250 } },
      { sal: { $gt: 1600 } }
    ]
  },
  { _id: 0, ename: 1, sal: 1 }
)
8. db.emps.find(
  { job: { $nin: ["MANAGER", "PRESIDENT"] } }
)
9. db.emps.find(
  { ename: { $regex: /^.{2}A/ } },
  { _id: 0, ename: 1 }
)
10. db.emps.find(
  { comm: { $ne: null, $ne: 0 } },
  { _id: 0, empno: 1, ename: 1, job: 1 }
)
11. db.emps.find().sort({ comm: 1 })
12. db.emps.find(
  { job: { $nin: ["MANAGER", "PRESIDENT"] } }
).sort({ deptno: 1, hiredate: -1 })
13. db.emps.find(
  { ename: { $regex: /^.{6}$/ } },
  { _id: 0, ename: 1 }
)
14. db.emps.aggregate([
  { $match: { deptno: 30 } },
  {
    $project: {
      _id: 0,
      Angestellter_Tätigkeit: { $concat: ["$ename", "-", "$job"] }
    }
  }
])
15. Select SAL+COMM
	from emp (bedeutet dass null Werte wenn sie miteinander addiert werden das sie NULL zurückgibt anstatt eine ganze Zahl)
      Select SAL+NVL(COMM) 
	from emp (bedeutet das die NULL Werte als 0 berechnet werden und somit eine ganze lesbare Zahl dabei herauskommt)
16. db.emps.find() (

MongoDB for Macho
