

###Table of contents
[[toc]]

##Introduction
The comma seperated value format (CSV) has been used for exchanging and converting student data between various application for quite some time. Surprisingly, while this format is very common within education, it has never been formally documented.

##Overview
###Audience
This document is intended for technical users responsible for interoperability and data sharing between application in the pK-12 environment.

###Purpose
While numerous private specifications exist for various programs and systems, there is no single "master" specification for this format. This document provides specifications and guidance for developing formatted pK-12 student enrollment data. Due to lack of a single specification, there are considerable differences among implementations.

###Relationship of CSV
![data model](http://yuml.me/diagram/scruffy/class/ [SCHOOL-district]->[SECTION-course-term], [SECTION-course-term]->[TEACHER], [SCHOOL-district]->[ENROLLMENT], [SECTION-course-term]->[ENROLLMENT], [ENROLLMENT]->[STUDENT])

##CSV Import Format
The first record in a CSV file **must** be a header record containing field names. Field names are seperated by commas. Field names must match headings defined for each enrollment file type. An example of the header record is show below

> schoolId, sectionId, studentId 

After the header record, the CSV file is made up of one or more data records. A data record is one line, contains values in one or more fields, separated by commas. 

+ Data fields are seperated with commas.
> "John Doe", 2014-12-25 
+ Leading and trailing whitespace is ignored.
+ Data fields marked as Strings must be delimited with double-quote characters. 
> "John"
+ Data fields that contain double-quote characters must be surrounded by double-quotes.
> "John ""The Original"" Doe"
+ Data fields that contain commas characters must be surrounded by double-quores.
> "Apt 1234, 5th Street"
+ Data fields marked as Date must be formated as a ISO 8601 Date YYYY-MM-DD.
> 2014-12-25
+ Data fields marked as Boolean can have a value of "Yes", "No", "Unknown", or empty

##Parsing cells
You should review the W3C document "[Metadata Vocabulary for Tabular Data](http://www.w3.org/TR/2014/WD-tabular-metadata-20140710/#parsing-cells)" section 3.8.5 Parsing cells. 

##schools.csv
The school.csv collects school level information catagorized by district name and contains one or many schools. If the NCESId is provided you can collect additional information about the school and district by searching the [NCES data collection](http://nces.ed.gov/ccd/schoolsearch/) with the provided NCESId  

![course->section](http://yuml.me/diagram/scruffy/class/[district]<>1->*[school])

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Length |
|:-:|:------|:---|:---:|:---:|:---:|
|1|schoolId| **(REQUIRED)** School's local id and must be unique across the district. Must not change. **(Unique)(Key)**| String|
|2|schoolName| **(REQUIRED)** The name of the school.|String|
|3|schoolFocus|The type of educational institution as classified by its focus <ul><li>"Regular"</li><li>"SpecialEd"</li><li>"Vocational"</li><li>"Alternative"</li><li>"Magnet"</li><li>"Charter"</li><li>"Private"</li></ul>|enum|
|4|title1Status|Status of the school's Title 1 elegibitily <ul><li>"Targeted"</li><li>"SchoolWide"</li><li>"NA"</li></ul>|enum|
|5|districtName| The district name for the school record.|String|
|6|NCESId| 12 digit federal identication number assigned by the National Center for Education Statistics for this school record.|String|
|7|schoolAddress| School's address|String|
|8|schoolCity| The city part of the school's address|String|
|9|schoolState| Two letter addreviation for the state.|String|
|10|schoolZip| 5 or 9 digit zip/postal code, with no punctuation.|Integer|
|11|schoolPhone| 10 digit phone number, with no punctuation.|Integer|
|12|lowGrade| The lowest grade served at this school.<ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul> |String|
|13|highGrade|The highest grade served at this school. <ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul>|String|
|14|contactName| The name of the principal at this school|String|
|15|contactEmail| The email address of the principal at this school|String|


##section.csv
The section.csv file provides a cross section of course, section, and term. Within SIF, Ed-Fi, and CEDS these would be normalized into seperate objects. We have denormalized the relatinship to simplify the delivery of data.

![course->section](http://yuml.me/diagram/scruffy/class/[course]<>1->*[section])

###Section Use Case:
+ Base Rule
> Students do not attend courses, they attend sections of a course.
+ Elementary
> Elementary student typically attend an all day class which cover multiple subjects. You can find this data represented as follows: 
> >1. The student will attend a course with a single section for each student.
> >2. The student will attend a courses with multiple sections for each student.
> >3. The student will attend multiple course with multiple section for each student. 

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Length |
|:-:|:------|:---|:---:|:---:|:---:|
|1|schoolId| **(REQUIRED)** Token from the school.csv table linking the course/section to a school. **(Key)**|String|
|2|sectionId|**(REQUIRED)** Sections Id and must be uniq across the district. Must not change. **(Unique)(Key)**|String|
|3|schoolYear| The School year for which the information is applicable, format "YYYY", e.g. 2014 for 2013-14|String|
|4|teacherId|Token from the teacher.csv table linking the teacher id to a section.|String|
|5|sectionName|**(REQUIRED)** The Name of the Section for this Course.|String|
|6|sectionGradeLevel|The grade level of this section. <ul><li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul>|String|
|7|courseName|The course name for this section.|String|
|8|courseNumber|The district-defined course code for this section|String|
|9|stateCourseNumber|The state-defined course code for this section|String|
|9|Period|The bell schedule information for section.|String|
|10|Subject|The subject matter area for this section.|String|
|11|termName|The text based name for the Term this section resides in.|String|
|12|termStartDate|The Section end date. The format should be in "YYYY-MM-DD"|date|
|13|termEndDate|The Section end date. The format should be in "YYYY-MM-DD"|date|

##student.csv
The student.csv provides details about each of the students within our enrollment records.

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Length |
|:-:|:------|:---|:---:|:---:|:---:|
|1|studentId| **(REQUIRED)** Student's local id and must be unique across the district. Must not change. **(Unique)(Key)**|String|
|2|studentStateId|Student's state id and must be unique across the district. **(Unique)**|String|
|3|firstName| **(REQUIRED)** The given name of the student |String|
|4|middleName|The middle name of the student|String|
|5|lastName| **(REQUIRED)** The last name of the student|String|
|6|studentUsername|The student's username for applications. |String|
|7|studentPassword|The student's default passwords for applications. |String|
|8|studentEmail|The Email address of the student|String|
|9|gender|A student's gender. <ul><li>"Male"</li><li>"Female"</li><li>"Unknown"</li></ul>|enum|
|10|DOB|This student's date of birth. The format should be ISO 8601 Date "YYYY-MM-DD"|String|
|11|gradeLevel|The grade level of the student. <ul>Use:<li>"PK" for Pre-Kindergarten/Preschool</li><li>"KG" for Kindergarten</li><li>integers for grades 1-12</li><li>"Other"</li><li>"Unknown"</li></ul>|String|
|12|race|The general racial category of the individual student<ul><li>"American Indian or Alaska Native"</li><li>"Asian"</li><li>"Black"</li><li>"Native Hawaiian or Other Pacific Islander"</li><li>"White"</li></ul>|String|
|13|hispanicLatino|An indication that the individual traces their origin or descent to Mexico, Puerto Rico, Cuba, Central or South America, or other Spanish cultures, regardless of race. <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean|
|14|ELL|Is the student an English Language Leaner under Title 3? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean|
|15|IDEA|Is the student IDEA eligible?  <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean|
|16|section504|Is this student Section 504 eligible? <ul><li>"Yes"</li><li>"No"</li></ul> |Boolean|
|17|IEP|Is this student IEP eligible? <ul><li>"Yes"</li><li>"No"</li></ul> |Boolean|
|18|homeless|Is the student homeless? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean|
|19|migrant|Is this a migrant student? <ul><li>"Yes"</li><li>"No"</li></ul>|Boolean|
|20|FRL|This student's lunch status? <ul><li>"Free"</li><li>"Reduced"</li><li>"No"</li></ul>|enum|


##teacher.csv
The teacher.csv provides details about each of the teachers within our enrollment records.

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Length |
|:-:|:------|:---|:---:|:---:|:---:|
|1|teacherId| **(REQUIRED)** Teacher's local id and must be unique across the district. Must not change. Can be from the source system or state id. **(Unique)** **(Stable)** **(Key)** |String|
|2|teacherStateId|Student's state id and must be unique across the district. **(Unique)**|String|
|3|firstName| **(REQUIRED)** The first name of the teacher. |String|
|4|middleName|The middle name of the teacher. |String|
|5|lastName| **(REQUIRED)** The given name of the teacher. |String|
|6|teacherTitle|Title for this teacher.|String|
|7|teacherUsername|The teacher's username for applications. |String|
|8|teacherPassword|The teacher's default passwords for applications. |String|
|9|teacherEmail|The Email address of the teacher|String|



##enrollment.csv
This file defines the information related to a students enrollmentin a section of a course.

|Order|Column| POSSIBLE VALUES & REMARKS | Type | Length |
|:-:|:------|:---|:---:|:---:|:---:|
|1|schoolId| **(REQUIRED)** Must match a schoolId from the schools.csv file. **(Unique)** **(Key)**|String|
|2|sectionId| **(REQUIRED)** Must match a sectionId from the section.csv file. **(Unique)** **(Key)**|String|
|3|studentId| **(REQUIRED)** Must match a studentId from the student.csv file. **(Unique)** **(Key)**|String|