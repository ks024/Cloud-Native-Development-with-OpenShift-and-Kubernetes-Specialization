#### Running local python script within the docker container
```bash
docker run -it -v $(pwd):/app python:3.9 python /app/exercise_password_generator.py
```
*This command run's the exercise_password_generator python file inside the python:3.9 container.*

**Explanation:**

* `docker run -it`: This launches the container in interactive mode.
* `-v $(pwd):/path/in/container`: This is the volume mounting option. 

**Let's break it down:**
- `-v`: This flag tells Docker to mount a volume.
- `$(pwd)`: This is a command substitution that gets replaced with the absolute path of your current working directory.
- `:/path/in/container`: This specifies two paths separated by a colon (:).
- The first path (left of the colon) is the source directory on your local machine, which is your current directory `$(pwd)` in this case.
- The second path (right of the colon) is the destination directory inside the container where you want the local directory mounted.