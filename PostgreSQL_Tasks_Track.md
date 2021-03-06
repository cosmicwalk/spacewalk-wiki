

For up-to-date information about the PostgreSQL port, see [[PostgreSQL]]. This page is now obsolete.

----
## Project Tasks



Project tasks are work items that need to be tracked.  They are different then issues in that items listed here
simply need to be completed and are not necessarily risks to the project.
### Kick off




| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- |
| K1 | Initial Project Meeting | Meeting of the proposed team members to create objectives, plan for the initial project plan. |  done  | All Team Members |    |  |
| K2 | Identify the Objectives of the Project | Create a detail project Objective to be achieved for the project including including Timeline and resources. |  done  | All Team Members |    |  |
| K3 |  Tools and Processes | Identify the tools and processes to be used through the project -- these tools include -- dev, test, track, plan, communication |  done  | All Team Members |  performance testing tools -- still open   |
| K4 |  Team | Identify the various roles in the project and assign potential members for the same |  done  | All Team Members |    |  |
### Planning/Analysis



`TimeLine`: Begin: Jan 2009 End: 13th Feb 2009


| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| P1 | Project Plan | Create the detailed project plan along with Tasks, Timelines and resources. |  done  | George W |   | 9th Feb  |   | on the development front we are good -- the Testing front we need more support |
| P2 | Development Technical Approach | Create the technical approach over all architecture for executing the project. |  done | Bruce M/Gurjeet |   | 13th Feb  |   | most of it is identified and the piece open is a set of function we need to port from Oracle  |
| P3 |  Test Technical Approach | Create the technical approach for Testing the application which includes -- functional, regression, performance, migrations and upgrades |  in progress  | George W/Vikram |  | 13th Feb  |   | need access and support from the Test Team   |
| P4 |  Oracle Sample Database | Creation of the sample database in Oracle for all related activities - including testing for migration, upgrades, functional and performance tests |  done  |  Jeff O |   | 13th Feb |   |  Small DB delivered 1/7. [DDL](https://fedorahosted.org/spacewalk/attachment/wiki/Postgreddl.sql.gz), Sample data available.  |
| P5 |  Reviews of Project plan and technical approach documents | Review the plan and the approaches with the whole team and get approvals |  done  |  Jeff O |  | 13th Feb  |   |   |  |
| P6 |  Sign-Off | Sign-off on the above iteration |  done  |  Todd S/Jeff O |   |  13th Feb |   |   |  |
| P7 |  Review next Iteration Plan | Review the plan for the next iteration including tasks, timelines  |  done  |  Todd S/Jeff O |   | 13th Feb |   |   |  |
### Iteration 1



`TimeLine`: Begin: Jan 2009 - End: 27 Feb 2009


| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I1 | Application Knowledge Transfer | A total overview of the complete application -- 1 day for total over view of the application.  Half day on functional walk through and half day schema review. |  done   | Jeff O |   | 13th Feb  |   |  Shooting for week of 1/26.   |
| I2 | Development Environment | Recommend the development environment. |  done  | Jeff O |   | 6th Feb  |   | EDB team Installed on CENTOS 5.  All issues resolved 1/21.  |
| I3 |  Test Environment | Recommend the required environment for the Test Lab | in progress   | Jeff O |   | 13th Feb   |   | need time with the Test Team  |
| I4 | Setup The Development Environment | Setup the development environments |  done  |  Team |   |  6th Feb  |   | see I2.  |
| I5 |  Setup The Test Lab | Setup the Test Environment |  in progress  |  Tushar/Vikram |   | 20th Feb  |   | access to the Test Team  |
| I6 | Identify Modules | Identify modules and prioritize them |  done  | Todd S/Jeff O |   | 13th Feb  |    |  modules identified - in iteration 2, [breakdown](PgIter2Breakdown) |
| I7 | Postgres Schema Generation | Create the whole schema for the application |  100%  |   |    | 20th Feb  |   |  process approved: now in converting [tables](PgportTables) |
| I8 | IMake Files | Creation of make files for schema generation |  in progress  |  done |   |  20th Feb  |   |  the approach has been identified and first iteration in place  |
| I9 | Orafce Testing | Identify Orafce usages and short comings and find all the required functionality |  done  |  Tushar/Vikram |   | 20th Feb   |   | identified a set of bugs -- working with the Orafce team on fixes as well as identified a set of features not available -- creating them |
| I10 | Database Access Layer | Identify mechanism to effectively to connect to the DB from the application |  in progress  |  dgoodwin  |   |  20th Feb  |    |   |  |
| I11 | Packaging Plan | Identify the packaging needs of the application |  in progress  |  dgoodwin  |   | 26th Feb  |   |   |  |
| I112 |  Review next Iteration Plan | Review the plan for the next iteration including tasks, timelines  |    |  Todd S/Jeff O |   |  27th Feb |   |    |
### Iteration 2



`TimeLine`: Begin: 2nd March 2009 - End: 30th April 2009


| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |


| M1 | Code Merge | Merge code with Head and resolve conflicts. |    | Jeff O |   |  6th March |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| I1 | Modules Freeze | Identify and freeze the set of modules we are going to work on for this iteration. |  in progress  | Jeff O |   |  6th March |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I2 | Modules Knowledge Transfer | Modules Knowledge Transfer and detailed Q&A. |    | Jeff O |   |  14th March  |   |   |  |


| D1 | Development: Schema Verification | Verify the schema in Pg for the identified modules. | schema - 100%, views - 100%, triggers - 100%   | Gurjeet/Vikram |   |  14th March |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D2 | Development: Query Identification | Identify the queries and validate them. |  Queries - tagged 100%  | Gurjeet/Vikram |   |  21st March |    |   |  |
| D3 | Development: Stored functions/procedures/packages  | Identify all the stored functions/packages/procedures .. and validate them. | done  | Gurjeet/Vikram |   |  21st March  |    |   |  |
| D4 | Development: Make the appropriate code Changes | Develop all the required code and validate them. |  Procedures - 100%, Packages - 100%  | Gurjeet/Vikram |   |  10th April |    |   |  |
| D5 | Development: Oracle to Postgres Migration | Generate the Oracle to Postgres Migration script. |    | Farrukh |   | 20th March  |   |   |  |
| D5 | Development: Postgres to Oracle Migration | Generate the Postgres to Oracle Migration script. |    | Farrukh |   |  17th April |   |   |  |


| T1 | Testing: Test Case Review | Review the existing Test cases for the modules. |    | Tushar/Vikram |   | 14th March  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T2 | Testing: Identify Gaps | Identify the gaps in the test cases |    | Tushar/Vikram |   | 14th March  |   |   |  |
| T3 | Testing: Develop Test Cases | Develop the test cases for the identified gaps. |    | Tushar/Vikram |   |  27th March |   |   |  |
| T4 | Testing: Review | Review the existing Test cases for the modules after adding the new ones. |    | Jeff O |   |  3rd April  |    |   |  |
| T5 | Testing: Build Automated Test Cases | Build and run the complete automated test suite. |    | Tushar/Vikram |   |  24th April  |   |   |  |
| T6 | Testing: Oracle to Postgres Migration | Test the Oracle to Postgres Migration. |    | Tushar/Vikram |   | 10th April  |   |   |  |
| T7 | Testing: Establish Oracle Performance Benchmarks | Execute and Establish the Oracle Benchmarks for the application. |    | Tushar/Vikram/Jeff O |   | 10th April  |   |   |  |
| T6 | Testing: Postgres to Oracle Migration | Test the Postgres to Oracle Migration. |    | Tushar/Vikram |   | 24th April  |   |   |  |



| I3 | Review & Sign-off above milestones | Review and Sign-off on the above tasks. |    | Todd S/Jeff O |   | 29th April  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I4 |  Review next Iteration Plan | Review the plan for the next iteration including tasks, timelines  |    |  Todd S/Jeff O |   | 29th April |   |   |  |
### Iteration 3



`TimeLine`: Begin: 1st May 2009 - End: 29th May 2009


| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |


| M1 | Code Merge | Merge code with Head and resolve conflicts. |    | Jeff O |   |  1st May |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| I1 | Modules Freeze | Identify and freeze the set of modules we are going to work on for this iteration. |    | Jeff O |   |  1st May  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I2 | Modules Knowledge Transfer | Modules Knowledge Transfer and detailed Q&A. |    | Jeff O |   |  8th May |   |   |  |


| D1 | Development: Schema Verification | Verify the schema in Pg for the identified modules. | schema - 100%, views - 100%, triggers - 100%   | Gurjeet/Vikram |   |  8th May  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D2 | Development: Query Identification | Identify the queries and validate them. |    | Gurjeet/Vikram |  Queries Tagged - 100% |  8th May |    |   |  |
| D3 | Development: Stored functions/procedures/packages  | Identify all the stored functions/packages/procedures .. and validate them. | Procedures - 100%, Packages - 100%   | Gurjeet/Vikram |   | 8th May  |   |   |  |
| D4 | Development: Make the appropriate code Changes | Develop all the required code and validate them. |  Procedures - 100%, Packages - 50%  | Gurjeet/Vikram |   | 22nd May |   |   |  |
| D5 ?? | Development: Upgrade Scripts between versions of Spacewalk | Generate the Pg plan/upgrade script for the application. |    | Farrukh |   | 22nd May  |   |   |  |


| T1 | Testing: Test Case Review | Review the existing Test cases for the modules. |    | Tushar/Vikram |   |  8th May |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T2 | Testing: Identify Gaps | Identify the gaps in the test cases |    | Tushar/Vikram |   |  8th May |   |   |  |
| T3 | Testing: Develop Test Cases | Develop the test cases for the identified gaps. |    | Tushar/Vikram |   |  15th May |    |   |  |
| T4 | Testing: Review | Review the existing Test cases for the modules after adding the new ones. |    | Jeff O |   |  22nd May |   |   |  |
| T5 | Testing: Build Automated Test Cases | Build and run the complete automated test suite. |    | Tushar/Vikram |   | 22nd May |   |   |  |
| T6 ?? | Testing: Upgrade Scripts between versions of Sapcewalk | Test the upgrade scripts migration. |    | Tushar/Vikram |   | 28th May  |   |   |  |
| T7 | Testing: Establish Pg Performance Benchmarks | Execute and Establish the Pg Performance Benchmarks for the developed modules. |    | Tushar/Vikram/Jeff O |   | 28th May  |   |   |  |


| I3 | Review & Sign-off above milestones | Review and Sign-off on the above tasks. |    | Todd S/Jeff O |   | 29th May  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I4 |  Review next Iteration Plan | Review the plan for the next iteration including tasks, timelines  |    |  Todd S/Jeff O |   | 29th May  |   |   |  |
### Iteration 4



`TimeLine`: Begin: 1st June 2009 - End: 30th June 2009



| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |


| M1 | Code Merge | Merge code with Head and resolve conflicts. |    | Jeff O |   | 3rd June |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |



| I1 | Modules Freeze | Identify and freeze the set of modules we are going to work on for this iteration. |     | Jeff O |   | 5th June  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I2 | Modules Knowledge Transfer | Modules Knowledge Transfer and detailed Q&A. |    | Jeff O |   | 5th June  |   |   |  |
| D1 | Development: Schema Verification | Verify the schema in Pg for the identified modules. |  Schema - 100%, Views - 100%, Triggers - 100%  | Gurjeet/Vikram |   | 5th June  |   |   |  |
| D2 | Development: Query Identification | Identify the queries and validate them. |  Queries Tagged - 0%  | Gurjeet/Vikram |   | 5th June |    |   |  |
| D3 | Development: Stored functions/procedures/packages  | Identify all the stored functions/packages/procedures .. and validate them. | Procedures - 100%, Packages - 100%   | Gurjeet/Vikram |   | 5th June  |   |   |  |
| D4 | Development: Make the appropriate code Changes | Develop all the required code and validate them. | Procedures - 100%, Packages - 50%   | Gurjeet/Vikram |   | 19th June |    |   |  |


| T1 | Testing: Test Case Review | Review the existing Test cases for the modules. |    | Tushar/Vikram |   | 5th June |    |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T2 | Testing: Identify Gaps | Identify the gaps in the test cases |    | Tushar/Vikram |   | 5th June |   |   |  |
| T3 | Testing: Develop Test Cases | Develop the test cases for the identified gaps. |    | Tushar/Vikram |   | 12th June |   |   |  |
| T4 | Testing: Review | Review the existing Test cases for the modules after adding the new ones. |    | Jeff O |   | 19th June |   |   |  |
| T5 | Testing: Build Automated Test Cases | Build and run the complete automated test suite. |    | Tushar/Vikram |   | 26th June |   |   |  |
| T6 | Testing: Establish Pg Performance Benchmarks | Execute and Establish the Pg Performance Benchmarks for the developed modules. |    | Tushar/Vikram/Jeff O |   | 26th June  |   |   |  |


| I3 | Identify Performance Bottlenecks and other outstanding items | Identify and establish Performance/functional outliners and any other outstanding items |    | Todd S/Jeff O |   | 29th June  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| I4 | Review & Sign-off above milestones | Review and Sign-off on the above tasks. |    | Todd S/Jeff O |   | 30th June |   |   |  |
| I5 |  Review next Iteration Plan | Review the plan for the next iteration including tasks, timelines  |    |  Todd S/Jeff O |   | 30th June  |   |   |  |
### Final Iteration



`TimeLine`: Begin: 1st July 2009 - End: 31st July 2009



| *ID* | *Name* | *Description* | *Status* | *Assigned* | *Start Date* | *End Date* | *Dependency* | *Notes* |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |



| M1 | Code Merge | Merge code with Head and resolve conflicts. |    | Jeff O |   |  3rd July |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| I1 | Scope Freeze | Identify and freeze the set items identified for completion. |    | Jeff O |   |  3rd July  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| T1 | Testing: User Acceptance Tests | Complete the UAT -- which is part of the continuos iteration - this one should be the final pass |    | Jeff O |   | 10th July  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T2 | Testing: Pg Performance Benchmarks with fixes | Execute and Establish the Pg Performance Benchmarks for the application. |    | Tushar/Vikram/Jeff O |   | 24th July |   |   |  |


| D1 | Develop Guidelines for Database compatibility | Develop a set of guidelines for the general Database compatibility for future enhancements to the product. |    | Jeff O |   | 24th July  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| D2 | Develop Packaging | Build the packaging for the application. |    | Jeff O |   | 24th July  |   |   |  |
| D3 | Develop Performance Bottlenecks | Fine tune the application for the identified performances. |    | Gurjeet/Vikram |   | 17th July  |   |   |  |


| M2 | Final Code Merge | Merge code with Head and resolve conflicts. |    | Jeff O |   | 27th July |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| T2 | Testing:Package | Test the end package |    | Tushar/Vikram/Jeff O |   | 31st July |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |


| I2 | Review & Project Sign-off | Review the above as well as sign-off for the project Completion |    | Jeff O |   | 3rd August  |   |   |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |





