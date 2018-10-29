# TABLE OF CONTENT

- [SCHOOLS](#schools)
- [SECTIONS](#teacher)
- [STUDENT](#teacher)
- [TEACHERS](#teacher)
- [ENROLLMENT](#enrollent)

## Introduction

The comma separated value format (CSV) has been used for exchanging and converting student data between various applications for quite some time. Surprisingly, while this format is very common within education, it has never been formally documented.

## Overview

### Audience

This document is intended for technical users responsible for interoperability and data sharing between application in the pK-12 environment.

### Purpose

While numerous private specifications exist for various programs and systems, there is no single "master" specification for this format. This document provides specifications and guidance for developing formatted pK-12 student enrollment data. Due to lack of a single specification, there are considerable differences among implementations.

### Data Alignment

The data elements contained with this this specification has been aligned with the School Interoperability Framework US 3.1 Data Model. This means that CEDS and Ed-Fi can be easily mapped to if your are inclided to use there data models.

### Files

This standard contains five CSV files, with the following filenames:

|Name|Purpose| Required Fields |
|:-:|:------|:--|
|schools.csv| provides school level information| schoolId, schoolName|
|sections.csv| provides section level information| schoolId, sectionId, schoolYear, teacherId, sectionName|
|students.csv| provides student level information| studentId, firstName, lastName |
|teachers.csv| provides teacher level information| teacherId, firstName, lastName |
|enrollments.csv| provides student enrollment information| schoolId, sectionId, studentId |

### Relationship of CSV files

![data model](http://yuml.me/diagram/scruffy/class/[SCHOOL.CSV]->[SECTION.CSV],[SECTION.CSV]->[TEACHER.CSV],[SCHOOL.CSV]->[ENROLLMENT.CSV],[SECTION.CSV]->[ENROLLMENT.CSV],[ENROLLMENT.CSV]->[STUDENT.CSV])

## Data Classification

Criteria to determine data classification levels for student enrollment data is contained below.  However you should review NIST special publication [800-122](http://csrc.nist.gov/publications/nistpubs/800-122/sp800-122.pdf) "Guide to Protecting the Confientiality of Personally Identifiable Information (PII)".

|Classification|Description| U.S. GOV Equivalent|
|:--|:---|--|
| Public | any information that was not considered "Internal", "Restricted", or "Hazardous". | Public |
| Internal | any information that is not generally available to the public, but my be subject to open records disclosure.| Sensitive |
| Restricted | any information that otherwise qualifies as "Hazardous", but would significantly reduce the effectiveness of faculty, staff, or students when acting in support of the districts mission.| Confidential |
| Hazardous | any information where the protection of the information is required by law/regulation or is required to self-report to the government and or provide notice to the individual if information is inappropriatedly accessed.| Confiential requiring special handling|

## CSV Import Format

The first record in a CSV file **must** be a header record containing field names. Field names are separated by commas. Field names must match headings defined for each enrollment file type. An example of the header record is show below

> schoolId, sectionId, studentId

After the header record, the CSV file is made up of one or more data records. A data record is one line, contains values in one or more fields, separated by commas.

- Data fields are seperated with commas.
    > "John Doe", 2014-12-25
- Leading and trailing whitespace is ignored.
- Data fields marked as Strings must be delimited with double-quote characters.
    > "John"
- Data fields that contain double-quote characters must be surrounded by double-quotes.
    > "John ""The Original"" Doe"
- Data fields that contain commas characters must be surrounded by double-quores.
    > "Apt 1234, 5th Street"
- Data fields marked as Date must be formatted as a ISO 8601 Date YYYY-MM-DD.
    > 2014-12-25
- Data fields marked as Boolean can have a value of "Yes", "No", "Unknown", or empty

### Parsing cells

You should review the W3C document "[Metadata Vocabulary for Tabular Data](http://www.w3.org/TR/2014/WD-tabular-metadata-20140710/#parsing-cells)" section 3.8.5 Parsing cells.

<a id='schools'></a>

## schools

The school.csv collects school level information catagorized by district name and contains one or many schools. If the NCESId is provided you can collect additional information about the school and district by searching the [NCES data collection](http://nces.ed.gov/ccd/schoolsearch/) with the provided NCESId. This objects transmission confientiality would be low.  

![course->section](http://yuml.me/diagram/scruffy/class/[district]<>1->*[school])

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Classification |
|:-:|:------|:---|:---:|:--:|:--:|:--|:--|
|1|schoolId| **(REQUIRED)** School's local id and must be unique across the district. Must not change. **(Unique)(Key)**| VARCHAR(100) | INTERNAL |
|2|schoolName| **(REQUIRED)** The name of the school.|VARCHAR(100)| PUBLIC |
|3|schoolFocus|The type of educational institution as classified by its focus <ul><li>"Regular"</li><li>"SpecialEd"</li><li>"Vocational"</li><li>"Alternative"</li><li>"Magnet"</li><li>"Charter"</li><li>"Private"</li></ul>|ENUM| PUBLIC |
|4|title1Status|Status of the school's Title 1 elegibitily <ul><li>"Targeted"</li><li>"SchoolWide"</li><li>"NA"</li></ul>|ENUM| PUBLIC |
|5|districtName| The district name for the school record.|VARCHAR(100)| PUBLIC |
|6|NCESId| 12 digit federal identication number assigned by the National Center for Education Statistics for this school record.|VARCHAR(12)| PUBLIC |
|7|address| School's address|VARCHAR(100)| PUBLIC |
|8|city| The city part of the school's address|VARCHAR(100)| PUBLIC |
|9|state| Two letter addreviation for the state.|VARCHAR(2)| PUBLIC |
|10|zip| 5 or 9 digit zip/postal code, with no punctuation.|VARCHAR(9)| PUBLIC |
|11|phone| 10 digit phone number, with no punctuation.|VARCHAR(10)| PUBLIC |
|12|lowGrade| The lowest grade served at this school. <ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul> |VARCHAR(10)| PUBLIC |
|13|highGrade|The highest grade served at this school. <ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul> |VARCHAR(10)| PUBLIC |
|14|contactName| The name of the principal at this school|VARCHAR(100)| PUBLIC |
|15|contactEmail| The email address of the principal at this school.|VARCHAR(100)| PUBLIC |

<a id='section'></a>

## section

The section.csv file provides a cross section of course, section, and term. Within SIF, Ed-Fi, and CEDS these would be normalized into seperate objects. We have denormalized the relatinship to simplify the delivery of data. This objects transmission confientiality would be low.

![course->section](http://yuml.me/diagram/scruffy/class/[course]<>1->*[section])

### Section Use Case

- Base Rule
> Students do not attend courses, they attend sections of a course.
- Elementary
> Elementary student typically attend an all day class which cover multiple subjects. You can find this data represented as follows:
> >1. The student will attend a course with a single section for each student.
> >2. The student will attend a courses with multiple sections for each student.
> >3. The student will attend multiple course with multiple section for each student.

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Classification |
|:-:|:------|:---|:---:|:--:|:--:|:--|:--|
|1|schoolId| **(REQUIRED)** Token from the school.csv table linking the course/section to a school. **(Key)**|String| INTERNAL |
|2|sectionId|**(REQUIRED)** Sections id and must be uniq across the district. Must not change. **(Unique)(Key)**|String| INTERNAL |
|3|schoolYear|**(REQUIRED)** The school year for which the information is applicable, format "YYYY", e.g. 2014 for 2013-14| String | PUBLIC |
|4|teacherId|**(REQUIRED)**  Token from the teacher.csv table linking the teacher id to a section.|String| RESTRICTED |
|5|sectionName|**(REQUIRED)** The name of the section for this course.|String| PUBLIC |
|6|sectionGradeLevel|The grade level of this section. <ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul>|String| PUBLIC |
|7|courseName|The course name for this section.|String| PUBLIC |
|8|courseNumber|The district-defined course code for this section|String| PUBLIC |
|9|stateCourseNumber|The state-defined course code for this section|String| PUBLIC |
|9|Period|The bell schedule information for section.|String| PUBLIC |
|10|Subject|The subject matter area for this section.|String| PUBLIC |
|11|termName|The text based name for the Term this section resides in.|String| PUBLIC |
|12|termStartDate|The Section end date. The format should be in "YYYY-MM-DD"|date| PUBLIC |
|13|termEndDate|The Section end date. The format should be in "YYYY-MM-DD"|date| PUBLIC |

<a id='student'></a>

## student

The student.csv provides details about each of the students within our enrollment records. This objects transmission confientiality would be high.  

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Classification |
|:-:|:------|:---|:---:|:--:|
|1|studentId| **(REQUIRED)** Student's local id and must be unique across the district. Must not change. **(Unique)(Key)**|String| RESTRICTED |
|2|studentStateId|Student's state id and must be unique across the district. **(Unique)**|String| RESTRICTED |
|3|firstName| **(REQUIRED)** The given name of the student |String| RESTRICTED |
|4|middleName|The middle name of the student|String| RESTRICTED |
|5|lastName| **(REQUIRED)** The last name of the student|String| RESTRICTED |
|6|username|The student's username for applications access. |String| RESTRICTED |
|7|password|The student's default passwords for applications authentication. If you are using SAML you can leave blank. |String| RESTRICTED |
|8|email|The Email address of the student|String| RESTRICTED |
|9|gender|A student's gender. Full words. <ul><li>"Male"</li><li>"Female"</li><li>Unknown</li></ul>|enum| RESTRICTED |
|10|DOB|This student's date of birth. The format should be ISO 8601 Date "YYYY-MM-DD"|String|  RESTRICTED |
|11|gradeLevel|The grade level of the student. <ul>Use:<li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul>|String|  RESTRICTED |
|12|race|The general racial category of the individual student<ul><li>"American Indian or Alaska Native"</li><li>"Asian"</li><li>"Black"</li><li>"Native Hawaiian or Other Pacific Islander"</li><li>"White"</li></ul>|String|  RESTRICTED |
|13|hispanicLatino|An indication that the individual traces their origin or descent to Mexico, Puerto Rico, Cuba, Central or South America, or other Spanish cultures, regardless of race. <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean| RESTRICTED |
|14|ELL|Is the student an English Language Leaner under Title 3? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean| RESTRICTED |
|15|IDEA|Is the student IDEA eligible?  <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean| RESTRICTED |
|16|section504|Is this student Section 504 eligible? <ul><li>"Yes"</li><li>"No"</li></ul> |Boolean| RESTRICTED |
|17|IEP|Is this student IEP eligible? <ul><li>"Yes"</li><li>"No"</li></ul> |Boolean| RESTRICTED |
|18|homeless|Is the student homeless? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean| RESTRICTED |
|19|migrant|Is this a migrant student? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean| RESTRICTED |
| 20 | FRL | This student's lunch status<ul><li>"Free"</li><li>"Reduced"</li><li>No</li></ul>| enum | RESTRICTED |
|21|gradYear|This is the year the student will graduate. The format is a four digit year, ISO 8601 Date "YYYY"|String| RESTRICTED |

<a id='teacher'></a>

## teacher.csv

The teacher.csv provides details about each of the teachers within our enrollment records. This objects transmission confientiality would be high.  

| Order | Column | POSSIBLE VALUES & REMARKS | Type | Classification |
|:-:|:------|:---|:-:|:--:|:--:|:--|:--|
|1|teacherId| **(REQUIRED)** Teacher's local id and must be unique across the district. Must not change. Can be from the source system or state id. **(Unique)** **(Stable)** **(Key)** |String| INTERNAL |
|2|teacherStateId|Student's state id and must be unique across the district. **(Unique)**|String| INTERNAL |
|3|firstName| **(REQUIRED)** The first name of the teacher. |String| RESTRICTED |
|4|middleName|The middle name of the teacher. |String| RESTRICTED |
|5|lastName| **(REQUIRED)** The given name of the teacher. |String| RESTRICTED |
|6|title|Title for this teacher.|String| PUBLIC |
|7|username|The teacher's username for applications login. |String| RESTRICTED |
|8|password|The teacher's default passwords for applications authentication. If you are using SAML you can leave blank.  |String| RESTRICTED |
|9|email|The Email address of the teacher|String| PUBLIC |

<a id='enrollment'></a>

## enrollment

This file defines the information related to a students enrollmentin a section of a course. This objects transmission confientiality would be low.  

| Order | Column | POSSIBLE VALUES & REMARKS | Type | Classification |
|:-:|:------|:---|:---:|:--:|:--:|:--|:--|
| 1 | schoolId | **(REQUIRED)** Must match a schoolId from the schools.csv file. **(Unique)** **(Key)**|String| INTERNAL |
| 2|sectionId| **(REQUIRED)** Must match a sectionId from the section.csv file. **(Unique)** **(Key)**|String| INTERNAL |
| 3|studentId| **(REQUIRED)** Must match a studentId from the student.csv file. **(Unique)** **(Key)**|String| RESTRICTED |