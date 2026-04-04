# Test Suite : CourseEnrollmentService.enrollCourseOfferingForStudent

## Scenario 1: Diluar periode KRS

Asumsi periode KRS semester 2025F = 1 Maret 2026 - 21 Maret 2026

### Arrange

```
CourseEnrollmentService.courseEnrollRange = 1 Maret 2026 - 21 Maret 2026
CourseEnrollmentService.currentDate = 22 Maret 2026
courseOffering = CourseOffering(
  semesterID: "2025F",
  course: Course(name: "AKPSI", credit: 3)
)
student = Student(id: "0001", name: "Fulan")
```

### Act

```
enrollmentService.enrollCourseOfferingForStudent(courseOffering, student)
```

### Assert

```
message = "periode telah berakhir"
```

## Scenario 2: MK Prasyarat belum diambil

### Arrange

```
CourseEnrollmentService.courseEnrollRange = 1 Maret 2026 - 21 Maret 2026
CourseEnrollmentService.currentDate = 12 Maret 2026

courseOffering = CourseOffering(
  semesterID: "2025F",
  course: Course(
    name: "Manajemen Data Lanjut",
    credit: 3,
    code: "MD-002",
    prerequisite: "MD-001"
  )
)

student = Student(
  id: "0001",
  name: "Fulan",
  coursePlans: [
    CoursePlan(
      semester: "2025F",
      maximumCreditAllowed: 9,
      courses: [
        Course(
          name: "AKPSI",
          credit: 3,
          code: "AP-001",
          prerequisite: nil
        )
      ]
    )
  ]
)
```

### Act

```
enrollmentService.enrollCourseOfferingForStudent(courseOffering, student)
```

### Assert

```
message = "MK prasyarat belum diambil"
```

## Scenario 3: Limit telah maksimal

### Arrange

```
CourseEnrollmentService.courseEnrollRange = 1 Maret 2026 - 21 Maret 2026
CourseEnrollmentService.currentDate = 12 Maret 2026

courseOffering = CourseOffering(
  semesterID: "2025F",
  course: Course(
    name: "Manajemen Data Lanjut",
    credit: 3,
    code: "MD-002",
    prerequisite: "MD-001",
    schedule: "Senin, 19.00 - 21.30"
  )
)

student = Student(
  id: "0001",
  name: "Fulan",
  coursePlans: [
    CoursePlan(
      semester: "2025F",
      maximumCreditAllowed: 9,
      courses: [
        Course(
          name: "AKPSI",
          credit: 3,
          code: "AP-001",
          prerequisite: nil,
          schedule: "Senin, 19.00 - 21.30"
        ),
        Course(
          name: "Manajemen Data",
          credit: 3,
          code: "MD-001",
          prerequisite: nil,
          schedule: "Selasa, 19.00 - 21.30"
        ),
        Course(
          name: "Infra TI",
          credit: 3,
          code: "IT-001",
          prerequisite: nil,
          schedule: "Rabu, 19.00 - 21.30"
        )
      ]
    )
  ]
)
```

### Act

```
enrollmentService.enrollCourseOfferingForStudent(courseOffering, student)
```

### Assert

```
message = "SKS telah mencapai batas maksimal"
```

## Scenario 4: Skedul overlap

### Arrange

```
CourseEnrollmentService.courseEnrollRange = 1 Maret 2026 - 21 Maret 2026
CourseEnrollmentService.currentDate = 12 Maret 2026

courseOffering = CourseOffering(
  semesterID: "2025F",
  course: Course(
    name: "Manajemen Data Lanjut",
    credit: 3,
    code: "MD-002",
    prerequisite: "MD-001",
    schedule: "Senin, 19.00 - 21.30"
  )
)

student = Student(
  id: "0001",
  name: "Fulan",
  coursePlans: [
    CoursePlan(
      semester: "2025F",
      maximumCreditAllowed: 9,
      courses: [
        Course(
          name: "AKPSI",
          credit: 3,
          code: "AP-001",
          prerequisite: nil,
          schedule: "Senin, 19.00 - 21.30"
        ),
        Course(
          name: "Manajemen Data",
          credit: 3,
          code: "MD-001",
          prerequisite: nil,
          schedule: "Selasa, 19.00 - 21.30"
        )
      ]
    )
  ]
)
```

### Act

```
enrollmentService.enrollCourseOfferingForStudent(courseOffering, student)
```

### Assert

```
message = "ada jadwal conflict"
```

## Scenario 5: Happy flow

### Arrange

```
CourseEnrollmentService.courseEnrollRange = 1 Maret 2026 - 21 Maret 2026
CourseEnrollmentService.currentDate = 12 Maret 2026

courseOffering = CourseOffering(
  semesterID: "2025F",
  course: Course(
    name: "Manajemen Data Lanjut",
    credit: 3,
    code: "MD-002",
    prerequisite: "MD-001",
    schedule: "Senin, 19.00 - 21.30"
  )
)

student = Student(
  id: "0001",
  name: "Fulan",
  coursePlans: [
    CoursePlan(
      semester: "2025F",
      maximumCreditAllowed: 9,
      courses: [
        Course(
          name: "Manajemen Data",
          credit: 3,
          code: "MD-001",
          prerequisite: nil,
          schedule: "Selasa, 19.00 - 21.30"
        )
      ]
    )
  ]
)
```

### Act

```
enrollmentService.enrollCourseOfferingForStudent(courseOffering, student)
```

### Assert

```
message = "MK telah ditambahkan pada KRS"
```
