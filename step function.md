# Design Patterns for AWS Step Functions

## Overview

This repository contains learning notes, examples, and implementation references for **Design Patterns for AWS Step Functions**.

The course focuses on common architectural patterns that can be implemented using **AWS Step Functions** to build reliable, scalable, and maintainable workflows.

## Course Structure

### Module 1: Introduction to Design Patterns for AWS Step Functions

* How to use this course
* Course introduction
* Introduction to design patterns for AWS Step Functions

### Module 2: Common Design Patterns

This module covers the most commonly used design patterns with AWS Step Functions:

* **Parallelism** — Execute multiple workflow branches concurrently.
* **Dynamic Parallelism** — Dynamically process multiple items using workflow iterations.
* **Nested Workflows** — Invoke one Step Functions workflow from another.
* **Orchestration and Choreography** — Coordinate distributed services using centralized or event-driven approaches.
* **Saga Pattern** — Manage distributed transactions using compensating actions.
* **Best Practices** — Guidelines for designing reliable and maintainable Step Functions workflows.

### Module 3: Demonstration — Implementing the Saga Design Pattern

A practical demonstration of implementing the **Saga design pattern** with AWS Step Functions.

The demonstration covers how to:

1. Coordinate multiple distributed operations.
2. Detect failures during workflow execution.
3. Execute compensating actions when required.
4. Maintain consistency across distributed services.

### Module 4: Conclusion

* Course wrap-up
* Next steps
* Key concepts and patterns reviewed throughout the course

### Module 5: Course Assessment

* Course assessment
* Feedback

## Key Concepts

The course focuses on the following concepts:

| Pattern             | Purpose                                         |
| ------------------- | ----------------------------------------------- |
| Parallelism         | Run independent tasks concurrently              |
| Dynamic Parallelism | Process a dynamically sized collection of items |
| Nested Workflows    | Compose workflows into reusable components      |
| Orchestration       | Centrally coordinate distributed services       |
| Choreography        | Coordinate services through events              |
| Saga                | Handle distributed transactions and failures    |

## AWS Step Functions

**AWS Step Functions** is a serverless workflow orchestration service that helps coordinate AWS services and applications into visual workflows.

Step Functions can be used to:

* Build multi-step workflows
* Coordinate microservices
* Handle retries and failures
* Run tasks in parallel
* Manage long-running processes
* Implement distributed transaction patterns
* Integrate with other AWS services

## Learning Objectives

After completing this course, you should have an understanding of:

* Common design patterns for AWS Step Functions
* When to use parallel and dynamic parallel execution
* How to create nested workflows
* Differences between orchestration and choreography
* How the Saga pattern handles distributed transactions
* Best practices for designing Step Functions workflows
* How to implement a Saga workflow in practice

## Course Completion

**Status:** 100% Complete

All modules and lessons have been completed, including the course assessment and feedback.

## Reference

This material is based on the AWS Training course:

**Design Patterns for AWS Step Functions**

© 2022 Amazon Web Services, Inc. or its affiliates. All rights reserved.

AWS trademarks and service names are the property of Amazon Web Services, Inc. or its affiliates.

## Disclaimer

This repository is intended for personal learning and educational purposes. AWS course content is subject to AWS licensing and usage restrictions. Course materials should not be reproduced or redistributed without appropriate permission.

## License

This README and any original notes or examples in this repository may be distributed under the repository's applicable license.

AWS course content remains the property of **Amazon Web Services, Inc. or its affiliates** and is subject to their terms and restrictions.
