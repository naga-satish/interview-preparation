# Comprehensive Docker Interview Questions by Topic

## Docker Fundamentals

1. What is Docker and why is it used?
2. What are the three main Docker components (Docker Client, Docker Host, Docker Registry)?
3. What is the difference between an image, container, and engine?
4. What is containerization and how does it differ from virtualization?
5. What is a hypervisor and how does it relate to Docker?
6. What are the advantages and disadvantages of using Docker?
7. What is a Docker daemon and what role does it play?
8. What is a Docker client and how does it communicate with the daemon?

## Docker Images

1. What is a Docker image and what does it contain?
2. How do you create a Docker image?
3. What is a Dockerfile and what is its purpose?
4. What are the best practices for reducing Docker image size?
5. What is the difference between base images and parent images?
6. How do you share Docker images across teams?
7. What are multi-stage Docker builds and why are they important?
8. How do you version Docker images?

## Docker Containers

1. What is a Docker container?
2. How is a Docker container different from a virtual machine?
3. What is the lifecycle of a Docker container?
4. How do you create and start a container?
5. How do you stop and remove a container?
6. What happens to data when a container is removed?
7. How do you inspect a running container?
8. How do you execute commands inside a running container?

## Dockerfile Instructions

1. What is the difference between CMD and RUN commands?
2. What is the difference between COPY and ADD commands?
3. What is the ENTRYPOINT instruction and when should you use it?
4. What is the difference between CMD and ENTRYPOINT?
5. What is the purpose of the WORKDIR instruction?
6. What is the ENV instruction used for?
7. What is the EXPOSE instruction and what does it do?
8. How do you use ARG for build-time variables?

## Docker Volumes and Data Persistence

1. How do you persist data in Docker containers?
2. What are Docker volumes and why are they important?
3. What is the difference between volumes and bind mounts?
4. How do you create and manage Docker volumes?
5. How do you share data between containers?
6. What happens to volumes when a container is deleted?
7. How do you backup and restore Docker volumes?
8. What are tmpfs mounts and when should you use them?

## Docker Networking

1. What are the different types of Docker networks?
2. How do containers communicate with each other?
3. What is a bridge network in Docker?
4. What is a host network and when should you use it?
5. What is an overlay network used for?
6. How do you expose container ports to the host?
7. How do you link containers together?
8. What is Docker DNS and how does it work?

## Docker Registry and Hub

1. What is a Docker registry?
2. What is Docker Hub and how do you use it?
3. How do you push images to a Docker registry?
4. How do you pull images from a registry?
5. What is the difference between public and private registries?
6. How do you set up a private Docker registry?
7. How do you tag Docker images for different registries?
8. What are image naming conventions in Docker?

## Docker Compose

1. What is Docker Compose and when should you use it?
2. How do you define services in a docker-compose.yml file?
3. What is the difference between docker-compose up and docker-compose start?
4. How do you scale services using Docker Compose?
5. How do you manage environment variables in Docker Compose?
6. How do you define dependencies between services?
7. What are Docker Compose networks and volumes?
8. How do you override configurations in Docker Compose?

## Docker Commands

1. What is the docker run command and what are its common options?
2. How do you list running containers?
3. How do you view logs from a container?
4. What is the difference between docker stop and docker kill?
5. How do you remove unused images and containers?
6. What is the docker exec command used for?
7. How do you inspect Docker objects (images, containers, volumes)?
8. What is the docker build command and its important flags?
9. How do you use docker ps to monitor containers?
10. What is the docker system prune command?

## Docker Security

1. What are Docker namespaces and how do they provide isolation?
2. What are control groups (cgroups) in Docker?
3. How do you implement security best practices in Docker?
4. What is the principle of least privilege in Docker?
5. How do you scan Docker images for vulnerabilities?
6. What are Docker secrets and how do you use them?
7. How do you run containers with non-root users?
8. What are Docker Content Trust and image signing?

## Docker Orchestration

1. What is Docker Swarm and what is it used for?
2. What is the difference between Docker Swarm and Kubernetes?
3. How do you create a Docker Swarm cluster?
4. What are Docker Swarm services and tasks?
5. How do you scale services in Docker Swarm?
6. What is a Docker stack?
7. How do you perform rolling updates in Docker Swarm?
8. What are Docker Swarm nodes (manager and worker)?

## Docker in CI/CD

1. How do you implement Docker in CI/CD pipelines?
2. What are the benefits of using Docker in CI/CD?
3. How do you automate Docker image builds in CI/CD?
4. How do you integrate Docker with Jenkins?
5. How do you use Docker with GitLab CI or GitHub Actions?
6. What are best practices for Docker in CI/CD workflows?
7. How do you perform automated testing with Docker?
8. How do you handle Docker image versioning in CI/CD?

## Performance and Optimization

1. How do you optimize Docker image build time?
2. What are Docker layer caching strategies?
3. How do you reduce Docker image size?
4. How do you monitor Docker container performance?
5. What are resource limits in Docker (CPU, memory)?
6. How do you troubleshoot slow container performance?
7. What is the impact of too many layers in Docker images?
8. How do you use .dockerignore files?

## Advanced Docker Concepts

1. What are multi-stage builds and how do they work?
2. How do you implement health checks in Docker?
3. What are Docker labels and metadata?
4. How do you use Docker BuildKit?
5. What is the difference between docker commit and Dockerfile?
6. How do you debug Docker containers?
7. What are Docker plugins and extensions?
8. How do you handle logging in Docker containers?

## Scenario-Based Questions

1. How would you migrate an application from VMs to Docker containers?
2. Describe a situation where you used Docker to solve a deployment problem.
3. How would you troubleshoot a container that keeps restarting?
4. How do you handle database containers and data persistence?
5. How would you set up a microservices architecture using Docker?
6. How do you upgrade a Docker container without downtime?
7. How would you debug networking issues between containers?
8. Describe your approach to containerizing a legacy application.
9. How do you handle configuration management across different environments?
10. How would you implement blue-green deployments using Docker?

Sources
1. [Top Docker Interview Questions and Answers (2025)](https://www.interviewbit.com/docker-interview-questions/)
2. [Top 26 Docker Interview Questions and Answers for 2025](https://www.datacamp.com/blog/docker-interview-questions)
3. [100+ Docker Interview Questions and Answers 2025](https://www.turing.com/interview-questions/docker)
4. [TOP Docker Interview Questions and Answers](https://www.youtube.com/watch?v=HHcgzhfuaWc)
5. [Day 21 - Docker Important Interview Questions](https://www.linkedin.com/pulse/day-21-docker-important-interview-questions-kartik-bhatt-xp5pc)
6. [Top 60+ Data Engineer Interview Questions and Answers](https://www.geeksforgeeks.org/data-engineering/data-engineer-interview-questions/)
7. [kunal-gohrani/docker-interview-questions](https://github.com/kunal-gohrani/docker-interview-questions)
8. [The Top 39 Data Engineering Interview Questions and ...](https://www.datacamp.com/blog/top-21-data-engineering-interview-questions-and-answers)
9. [Docker Interview Questions and Answers](https://www.vinsys.com/blog/docker-interview-questions-and-answers)
10. [Top 90+ Data Engineer Interview Questions and Answers](https://www.netcomlearning.com/blog/data-engineer-interview-questions)
11. [108 Docker interview questions](https://www.adaface.com/blog/docker-interview-questions/)
12. [52 Data Engineering Interview Questions With Sample ...](https://in.indeed.com/career-advice/interviewing/data-engineer-interview-questions)
13. [Top 50 Docker Interview Questions and Answers in 2025](https://www.edureka.co/blog/interview-questions/docker-interview-questions/)
14. [Interview Questions & Answers](https://www.ctanujit.org/uploads/2/5/3/9/25393293/data_engineering_interviews.pdf)
15. [14 Data Engineer Interview Questions and How to Answer ...](https://www.coursera.org/in/articles/data-engineer-interview-questions)
16. [15 Data Engineering Interview Questions & Answers](https://datalemur.com/blog/data-engineer-interview-guide)
17. [Data Engineer Interview Questions & Answers 2025](https://365datascience.com/career-advice/job-interview-tips/data-engineer-interview-questions/)
18. [What are the fundamental questions in a data engineering ...](https://www.reddit.com/r/dataengineering/comments/17ve0jc/what_are_the_fundamental_questions_in_a_data/)