# Assignment 2: Functional Design

## Problem Statement:

Every student deals with classwork; however, scheduling when to do work outside of class hours is tough when you are balancing other aspects of life. With every class having differing formats and even different ways to express the curriculum, finding the same piece of information can become arduous and tedious when needed to be done in repetition. The main issue students will face is managing all of their classes. Due to proper time management being an essential but hard to learn skill, many students will find it difficult to balance their schoolwork and other aspects of life. Students will always try to balance it all, but it can get tough or painful when some task slips through your fingers. Professors will judge a student's ability to manage their time as it will reflect on whether they turn in tasks properly on time. The student’s friends may even distract or add on to the list of things the student may want to do. them as they attempt to do their tasks, making it even tougher to manage everything.

How incoming college students can develop time-management skills https://www.ctinsider.com/waterbury/opinion/article/college-students-time-management-20379282.phpThis article talks about the crucial skill of time management

Planning Time Management in School Activities and Relation to Procrastination: A Study for Educational Sustainability https://www.mdpi.com/2071-1050/16/16/6883:

Canvas https://web.mit.edu/canvas/ Canvas is a tool used by various universities to provide students with information on their classes.

https://www.lifehack.org/692542/the-importance-of-time-management:
This article talks about the important way that time management skills are important, which emphasizes how students without them struggle more.

From my own experience, it can be tough to juggle schoolwork and my life, but thankfully I’ve been able to learn a few skills and improve.

## Application Pitch

I would like to introduce BrontoBoard - Sometimes you need a brontosaurus view to make decisions. BrontoBoard will act as a platform to showcase your classes and important dates of assignments and exams. This is all in order to aid students in organizing their schedules to improve their productivity. There would be a general board to summarize classes and highlight key problem sets and exams, a work calendar to help illustrate when work is due or when exam dates are, and a class overview page for when one needs to focus on a single class. Additionally, a user authentication process to separate the classes users take as each student has unique circumstances. The BrontoBoard, Calendersaurus, and AnkyloView each help the user in organizing their class work and visualize the work to better plan for it. This will impact primarily the students using the app but would impact the students schedules affecting their friends. Additionally, from better time management, improved performance in the classes is achieved, improving the way the teacher views the student.

## Concept Designs

### Concepts

    __concept:__ BrontoBoard
    __purpose:__ Associates set of Assignments, an overview, office hours, and a name to a class and that class to a BrontoBoard
    __principle:__ Each Assignment, overview, and Office Hours are associated with One Class. (Does not mean that Assignments, overviews, and office hours must be unique in every class), and each class can only belong to one BrontoBoard
    __state:__
    >   - a set of BrontoBoards with
            - a owner User
            - a Calendar
            - a set of Classes with
                - a name String
                - an overview String
                - a set of Assignments
                - a set of Office Hours
    __actions:__
        - __initializeBB__ (user: User, calendar: Calendar): BrontoBoard
            - __requires:__ A valid user and their calendar
            - __effects:__ Creates an empty BrontoBoard for the user
        - __createClass__ ( Classname: String, Overview: String): (class: Class)
            - __requires:__ the owner is valid and the Classname not be an empty String
            - __effects:__ Creates a class object assigned to the owner with the given information
        - __addWork__ (class: Class, workName: String, dueDate: Date): Assignment
            - __requires:__ owner and class are valid. workName and dueDate be not empty and dueDate be not before the current date
            - __effects:__  Create an Assignment under the Class of the owner with the given name and due date.
        -__changeWork:__ (work: Assignment, dueDate: Date):
            -__requires:__ A valid Assignment of a Class of the owner with a future date.
            -__effects:__ Modifies the Assignment to the new date
        -__removeWork:__ (work: Assignment)
            -__requires:__ A valid owner and existing Assignment
            -__effects:__ Removes the Assignment from its class
        -__addOH:__(class: Class, OHTime: Date, OHduration: Number): OfficeHours
            -__requires:__ A valid Assignment of a Class of the owner with a future date and non-negative OHDuration.
            -__effects:__ Modifies the Assignment to the new date
        -__changeOH:__ (user: User, oh: OfficeHours, newDate: Date, newduration: Number):
            -__requires:__ A valid user and oh and future newDate and non-negative newduration
            -__effects:__ Modifies the oh to the new date and duration


    __concept:__ WorkCalender
    __purpose:__ Associate an assignment or Exam to a day on a calendar
    __principle:__ Each assignment has one associated day.
    __state:__
    >    - a set of Calendars with
            - a set of Days with
                - a set of Assignments
                - a set of Office Hours
    __actions:__
        -__CreateCalendar__ (user: User): Calendar
            -__requires:__ a valid user
            -__effects:__ Creates an empty Calendar for the user
        -__assignWork__ (work: Assignemt)
            -__requires:__ a valid user with a created assignemnt
            -__effects:__ Assigns the Assignment to the appropriate date, when its due.
        -__removeWork:__ (work: Assignment)
            -__requires:__ A valid user and existing Assignment
            -__effects:__ Removes the Assignment from the calendar
        -__assignOH (oh: OfficeHours)
            -__requires:__ a valid user and oh
            -__effects:__ Assigns a oh to the appropriate day, when its happening.
        -__changeOH:__ (oh: OfficeHours, newDate: Date, newduration: Number):
            -__requires:__ A valid user and oh and future newDate and non-negative newduration
            -__effects:__ Modifies the oh to the new date and duration

    __concept:__ WorkExpirer
    __purpose:__ Deactivate work that was already due.
    __principle:__  when an assignment is given an expiry date, it will expire after the given time
    __state:__
        - a set of Assignments with
            - an expiration DateTime
    __actions:__
        -__setExpiry__ (work: Assignment, seconds: Number)
            -__effects:__associates an expiry seconds from now with Assignment
            __system__ expireWork () : (work: Assignment)
            __requires:__ expiry for the work is in the past
            __effect:__ returns the work and sets it to expired
        -__changeExpiry(work: Assignment, seconds: Number)
            -__effects:__ modifies the old expiry seconds to the new one with Assignment.

    __concept:__ UserAuthentication
    __purpose:__ Limit access of account to known users
    __principle:__ after a user registers with a username and a password,
    they can authenticate with that same username and password
    and be treated each time as the same user
    __state:__
    >   - a set of Users with
            - a username String
            - a password String
    __actions:__
        - __register__ (username: String, password: String): (user: User)
            - requires: A username and password of which neither has been taken before
            - effects: A new User is created with the same username and password
        - __authenticate__ (username: String, password: String): (user: User)
            - requires: the username and password both correspond to the same, existing User
            - effects: The User is allowed to enter

### Syncs

    - __sync:__   Assignment
        __when:__ BrontoBoardClasses.addwork(): Assignment
        __then:__
            - WorkCalendar.assignWork(Assignment)
            - WorkExpirer.setExpiry(Assignment, seconds till Assignment due date)

    - __sync:__   Expire
        __when:__ System.expireWork():Assignment
        __then:__
            - BrontoBoard.removeWork(Assignment)
            - WorkCalendar.removeWork(Assignemnt)

    - __sync:__   Tutorials
        __when:__ BrontoBoardClasses.addOH():OfficeHours
        __then:__ WorkCalendar.assignOH(OfficeHours)

    - __sync:__ Initialize
        __when:__
            - UserAuthentication.register():User
            - WorkCalendar.CreateCalendar(User):Calendar
        __then:__ WorkCalendar:
            - BrontoBoard.initiazlizeBB(User, Calendar)

### Why these Concepts?
Each concept plays a major role in provide users with a platform organizing their work. The BrontoBoard concept is the heart of the app. It does the key work of creating a unique, general board for each user, and it sets up the framework for the classes. The Workcalendar concept is what manages the user's calendar for use in providing a different view of when assignments are due. The WorkExpirer concept will clear the workload that was already due to clean up the data the user should focus on. The UserAuthentication concept will distinguish each user, allowing each user to personalize their boards to their needs.

## UI Sketches
![picture of my UI Sketches](assets/UISketches.png?format=jpg&name=4096x4096)

## User Journey
