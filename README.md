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

//neu ab hier

//17
db.emps.aggregate([
  {
    $group: {
      _id: null,
      totalSalary: { $sum: "$SAL" }
    }
  }
]);

//18
db.emps.aggregate([
  {
    $addFields: {
      commFixed: { $ifNull: ["$comm", 250] }
    }
  },
  {
    $group: {
      _id: null,
      durchschnittliche_prämie: { $avg: "$commFixed" }
    }
  }
])

//19
db.emps.aggregate([
  { $match: { dept_id: 30 } },
  {
    $project: {
      "CountOfSal": { $cond: { if: { $ne: ["$sal", null] }, then: 1, else: 0 } },
      "CountOfComm": { $cond: { if: { $ne: ["$comm", null] }, then: 1, else: 0 } }
    }
  },
  {
    $group: {
      _id: null,
      "CountOfSal": { $sum: "$CountOfSal" },
      "CountOfComm": { $sum: "$CountOfComm" }
    }
  }
])


//20
db.emps.aggregate([
  { $group: { _id: "$JOB" } },
  { $count: "distinctJobCount" }
])

//21
db.emps.aggregate([
  {
    $group: {
      _id: "$parent_id"
    }
  }
])

//21/2
db.emps.aggregate([
  {
    $match: {
      parent_id: { $ne: null }
    }
  },
  {
    $group: {
      _id: "$parent_id"
    }
  },
  {
    $count: "distinct_parent_id_count"
  }
])

//22
db.emps.aggregate([
  {
    $match: { dept_id: 30 }
  },
  {
    $group: {
      _id: null,
      SUM_SALARY: { $sum: "$SAL" },
      COUNT_SALARY: { $sum: { $cond: [{ $ne: ["$SAL", null] }, 1, 0] } },
      AVG_SALARY: {
        $avg: {
          $cond: [{ $ne: ["$SAL", null] }, "$SAL", null]
        }
      },
      SUM_COMMISSION: {
        $sum: { $ifNull: ["$COMM", 0] }
      },
      COUNT_COMMISSION: {
        $sum: { $cond: [{ $ne: ["$COMM", null] }, 1, 0] }
      },
      AVG_COMMISSION: {
        $avg: { $ifNull: ["$COMM", 0] }
      }
    }
  }
])

//23
db.emps.aggregate([
  {
    $match: {
      JOB: { $nin: ['MANAGER', 'PRESIDENT'] }
    }
  },
  {
    $group: {
      _id: "$dept_id",  // GROUP BY dept_id
      UNIQUE_JOBS: { $addToSet: "$JOB" }  // DISTINCT JOB
    }
  },
  {
    $project: {
      dept_id: "$_id",
      UNIQUE_JOBS: { $size: "$UNIQUE_JOBS" }  // Zählt die Anzahl der verschiedenen JOBs
    }
  }
]);

//24
db.emps.aggregate([
  {
    $group: {
      _id: "$dept_id",
      employee_count: { $sum: 1 }
    }
  },
  {
    $group: {
      _id: null,
      average_employee_count: { $avg: "$employee_count" }
    }
  }
])

//25
db.emps.find({
  JOB: { $in: ["MANAGER", "PRESIDENT"] }
})

//26
db.emps.aggregate([
  {
    $match: {
      $expr: {
        $gt: ["$COMM", { $multiply: [0.25, "$SAL"] }]
      }
    }
  },
  {
    $project: {
      _id: 0,
      ENAME: 1,
      JOB: 1,
      COMM: 1
    }
  }
])

//27
db.emps.aggregate([
  {
    $project: {
      totalCompensation: {
        $add: [
          "$SAL",
          { $ifNull: ["$COMM", 0] }
        ]
      }
    }
  },
  {
    $group: {
      _id: null,
      MIN_TOTAL_COMPENSATION: { $min: "$totalCompensation" }
    }
  },
  {
    $project: {
      _id: 0,
      MIN_TOTAL_COMPENSATION: 1
    }
  }
])

//28
db.emps.aggregate([
  {
    $group: {
      _id: null,
      OLDEST_EMPLOYEE_HIRED: { $min: "$HIREDATE" }
    }
  },
  {
    $project: {
      _id: 0,
      OLDEST_EMPLOYEE_HIRED: 1
    }
  }
])

//29
db.emps.aggregate([
  {
    $group: {
      _id: { dept_id: "$dept_id", JOB: "$JOB" },
      NUMBER_OF_EMPLOYEES: { $sum: 1 }
    }
  },
  {
    $sort: {
      "_id.dept_id": 1,
      "_id.JOB": 1
    }
  },
  {
    $project: {
      _id: 0,
      dept_id: "$_id.dept_id",
      JOB: "$_id.JOB",
      NUMBER_OF_EMPLOYEES: 1
    }
  }
]);

//30
db.emps.aggregate([
  {
    $group: {
      _id: "$dept_id",
      MAX_INCOME: {
        $max: {
          $add: [
            "$SAL",
            { $ifNull: ["$COMM", 0] }
          ]
        }
      }
    }
  },
  {
    $group: {
      _id: null,
      LOWEST_MAX_INCOME: { $min: "$MAX_INCOME" }
    }
  },
  {
    $project: {
      _id: 0,
      LOWEST_MAX_INCOME: 1
    }
  }
]);

//34
db.emps.aggregate([
  {
    $match: { dept_id: 30 }
  },
  {
    $group: {
      _id: null,
      MIN_SALARY: { $min: "$SAL" },
      MAX_SALARY: { $max: "$SAL" },
      AVG_SALARY: { $avg: "$SAL" },
      COUNT_SALARY: { $sum: { $cond: [{ $ne: ["$SAL", null] }, 1, 0] } },
      MIN_COMM: { $min: "$COMM" },
      MAX_COMM: { $max: "$COMM" },
      AVG_COMM: { $avg: "$COMM" },
      COUNT_COMM: { $sum: { $cond: [{ $ne: ["$COMM", null] }, 1, 0] } }
    }
  },
  {
    $project: {
      _id: 0,
      MIN_SALARY: 1,
      MAX_SALARY: 1,
      AVG_SALARY: 1,
      COUNT_SALARY: 1,
      MIN_COMM: 1,
      MAX_COMM: 1,
      AVG_COMM: 1,
      COUNT_COMM: 1
    }
  }
]);

//35
db.emps.aggregate([
  {
    $group: {
      _id: "$dept_id",
      MIN_SALARY: { $min: "$SAL" },
      MAX_SALARY: { $max: "$SAL" },
      AVG_SALARY: { $avg: "$SAL" }
    }
  }
])

//36
db.emps.aggregate([
  {
    $match: {
      JOB: { $nin: ["MANAGER", "PRESIDENT"] }
    }
  },
  {
    $group: {
      _id: "$dept_id",
      MIN_SALARY: { $min: "$SAL" },
      MAX_SALARY: { $max: "$SAL" },
      AVG_SALARY: { $avg: "$SAL" },
      EMPLOYEE_COUNT: { $sum: 1 }
    }
  }
])

select * from emps join depts on dept_id=DEPTNO;

db.emps.aggregate([
{
$lookup: {
localField: "dept_id",
from: "depts",
foreignField:"DEPTNO",
as: "department"
}
}]);

db.depts.aggregate([
  {
    $lookup: {
      localField: "DEPTNO",
      from: "emps",
      foreignField: "dept_id",
      as: "mitarbeiter"
    }
  }])

db.depts.aggregate([
  {
    $lookup: {
      localField: "DEPTNO",
      from: "emps",
      foreignField: "dept_id",
      as: "mitarbeiter"
    }
  },
  {
    $unwind: "$mitarbeiter"
  }
])

db.depts.aggregate([
  {
    $lookup: {
      localField: "DEPTNO",
      from: "emps",
      foreignField: "dept_id",
      as: "mitarbeiter"
    }
  },
  {
    $unwind: "$mitarbeiter"
  },
  {
    $project: {
      _id: 0,
      DEPTNO: 1,
      DNAME: 1,
      "mitarbeiter.ENAME": 1
    }
  }
])

//37
db.emps.aggregate([
  {
    $match: { COMM: null }
  },
  {
    $group: {
      _id: "$JOB",
      AVG_SALARY: { $avg: "$SAL" }
    }
  },
  {
    $project: {
      _id: 0,
      job: "$_id",
      AVG_SALARY: 1
    }
  }
])

//37
db.emps.aggregate([
  {
    $group: {
      _id: "$dept_id",
      TOTAL_ANNUAL_PAYMENT: {
        $sum: {
          $add: [
            { $multiply: ["$SAL", 12] },
            {
              $ifNull: [
                "$COMM",
                { $multiply: [100, 12] }
              ]
            }
          ]
        }
      }
    }
  },
  {
    $project: {
      _id: 0,
      dept_id: "$_id",
      TOTAL_ANNUAL_PAYMENT: 1
    }
  }
])

MongoDB for Macho
