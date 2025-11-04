# Commands and Arguments in Docker

- Take me to [Video Tutorial](https://kodekloud.com/topic/commands-and-arguments-in-docker/)

In this section, we will take a look at commands and arguments in docker

- To run a docker container

  ```
  docker run ubuntu
  ```

- To list running containers

  ```
  docker ps
  ```

- To list all containers including that are stopped

  ```
  docker ps -a
  ```

  This command will start a container and then immediately terminate:
  ![dc](../../images/dc.PNG)

Why is that?

#### Unlike virtual machines, containers are not meant to host operating system

- Containers are meant to run a specific task or process such as to host an instance of a webserver or application server or a database server etc.
- Once the task is complete, the container exits
- In the `docker run ubuntu` example, the specified command (if you look at the Dockerfile used to create the image) is `CMD ["bash"]`. `bash` looks for a terminal and if it cannot find any, it terminates.
  ![ex](../../images/ex.PNG)

#### How do you specify a different command to start the container?

- One Option is to append a command to the docker run command and that way it overrides the default command specified within the image.

  ```
  docker run ubuntu sleep 5
  ```

- This way when the container starts it runs the sleep program, waits for 5 seconds and then exists. How do you make that change permanent?

  ![sleep](../../images/sleep.PNG)

- There are different ways of specifying the command. Either the command simply as is in a shell form or in a JSON array format.

  ![sleep1](../../images/sleep1.PNG)

  _When using the JSON format, the first element of the JSON array is the command, followed by commandline argument as separate elements in the array._

- Now, build the docker image

  ```
  docker build -t ubuntu-sleeper .
  ```

- Run docker container

  ```
  docker run ubuntu-sleeper
  ```

  ![sleep2](../../images/sleep2.PNG)

- If we want to override the CMD["sleep", "5"] instruction, we'd use `docker run ubuntu-sleeper sleep 10`
- How can we just pass the amount of seconds to the sleep command without needing to use `sleep 10`?
  - Use `ENTRYPOINT["sleep"]` instead of `CMD["sleep", "10"]`, then you can do `docker run ubuntu-sleeper 10`
- How can we now define a default value to the ENTRYPOINT?
  - Use ENTRYPOINT["sleep"] together with CMD["5"]
- How do you modify the entrypoint during runtime?
  - Use the --entrypoint commandline option to the docker run command

## Entrypoint Instruction

- The entrypoint instruction is like the command instruction as in you can specify the program that will be run when the container starts and whatever you specify on the command line.

#### K8s Reference Docs

- <https://docs.docker.com/engine/reference/builder/#cmd>
