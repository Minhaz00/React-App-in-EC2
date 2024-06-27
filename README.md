# Deploy React app on EC2 instance using github actions

In this lab, you will learn how to automate the deployment of a React application to an Amazon EC2 instance using GitHub Actions. 

![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-17.png?raw=true)

GitHub Actions is a CI/CD tool integrated into GitHub, enabling you to automate workflows for building, testing, and deploying your code. 

By the end of this lab, you will have a fully automated pipeline that takes your React app from your GitHub repository to a running instance on AWS, ensuring continuous integration and continuous deployment.

## Setup React App locally

### Initialize React App

Let's start creating and styling a basic To-Do List application using React. The app will allow users to add and remove tasks, and we'll include some basic CSS for styling.

First, you need to create a new React application. Open your terminal and run the following command:

```bash
npx create-react-app todo-list-app
cd todo-list-app
```

This will create a new directory named `todo-list-app` and set up a new React project.

### Update `src/App.js`

Next, we need to replace the content of `src/App.js` with our To-Do List application code. Open `src/App.js` in your favorite code editor and replace its content with the following code:

```jsx
import React, { useState } from 'react';
import './App.css';

function App() {
  const [task, setTask] = useState('');
  const [tasks, setTasks] = useState([]);

  const handleAddTask = () => {
    if (task) {
      setTasks([...tasks, task]);
      setTask('');
    }
  };

  const handleRemoveTask = (index) => {
    const newTasks = tasks.filter((_, i) => i !== index);
    setTasks(newTasks);
  };

  return (
    <div className="App">
      <header className="App-header">
        <h1>To-Do List App</h1>
        <div className="task-input">
          <input
            type="text"
            value={task}
            onChange={(e) => setTask(e.target.value)}
            placeholder="Enter a task"
          />
          <button onClick={handleAddTask}>Add Task</button>
        </div>
        <ul className="task-list">
          {tasks.map((task, index) => (
            <li key={index}>
              {task} <button onClick={() => handleRemoveTask(index)}>Remove</button>
            </li>
          ))}
        </ul>
      </header>
    </div>
  );
}

export default App;
```

This code defines a simple To-Do List application with input handling, task addition, and task removal functionalities.

### Update `src/App.css`

To add some basic styling to our To-Do List app, replace the content of `src/App.css` with the following CSS code:

```css
.App {
  text-align: center;
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.task-input {
  margin: 20px;
}

.task-input input {
  padding: 10px;
  font-size: 1em;
  border: 2px solid #fff;
  border-radius: 5px;
  margin-right: 10px;
}

.task-input button {
  padding: 10px 20px;
  font-size: 1em;
  border: none;
  border-radius: 5px;
  background-color: #61dafb;
  color: #282c34;
  cursor: pointer;
}

.task-input button:hover {
  background-color: #21a1f1;
}

.task-list {
  list-style-type: none;
  padding: 0;
}

.task-list li {
  background-color: #61dafb;
  color: #282c34;
  padding: 10px;
  margin: 5px 0;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 300px;
}

.task-list li button {
  padding: 5px 10px;
  font-size: 0.8em;
  border: none;
  border-radius: 5px;
  background-color: #ff6347;
  color: white;
  cursor: pointer;
}

.task-list li button:hover {
  background-color: #ff2e1a;
}
```

This CSS code styles the app header, task input section, and the task list.

### Upload it in a GitHub Repository

1. **Create GitHub Repository**: Create a new repository for your Node.js project on GitHub. 
2. **Push Code to GitHub**: Initialize git, add remote origin, and push your code to the GitHub repository.


## Setup AWS EC2 Instance
1. **Login to AWS Console**: Login to your AWS account.
2. **Launch EC2 Instance**:
   - Choose "Ubuntu" as the operating system.
   - Ensure it's a free-tier eligible instance type.

        ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image.png?raw=true)

   - Create a new key pair or use an existing one.

        ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-1.png?raw=true)

3. **Configure Security Group**:
    - Go to security group > select the one for our instance > Edit inbound rules.
    - Allow SSH (port 22) from your IP.
    - Allow HTTP (port 80) from anywhere.

        ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-2.png?raw=true)

## Connect to EC2 Instance
1. **SSH into EC2 Instance**:
    - Go to your instance and click on `connect`.
    - Open terminal and navigate to the directory with your PEM file.
    - Use the SSH command provided by AWS to connect. We used windows powershell to SSH into EC2 Instance.

        ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-3.png?raw=true)


## Setup GitHub Actions Runner on EC2

### Setup GitHub Actions:

- Go to your repository’s settings on GitHub.
- Under “Actions”, click “Runners” and add a new self-hosted runner for Linux.

    ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-4.png?raw=true)

- Follow the commands provided to set up the runner on your EC2 instance.

    ![alt text](https://github.com/Minhaz00/React-App-in-EC2/blob/main/image/image-2.png?raw=true)

    You will see the `github action` console  after the final command (marked portion).

    After that, keep hitting `Enter` to continue with the default settings. Now if you go to the **github repository** > **settings** > **runners**, you will get something like this:

    ![alt text](https://github.com/Minhaz00/React-App-in-EC2/blob/main/image/image-4.png?raw=true)

    It is in offline state. Go to the SSH PowerShell and use the following command:

    ```bash
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

    Now the runner in the github repository is no more in Offline state:

    ![alt text](https://github.com/Minhaz00/React-App-in-EC2/blob/main/image/image-5.png?raw=true)






## Setup Pipeline with GitHub Actions

### Create GitHub Actions Workflow
   - In your repository, go to the “Actions” tab.
   - Choose the `Node.js` workflow template.
   - Configure/Change it as follows:

        ```yaml
        name: Node.js CI

        on:
          push:
            branches: [ "main" ]

        jobs:
          build:
            runs-on: self-hosted
            strategy:
              matrix:
                node-version: [18.x]

            steps:
            - uses: actions/checkout@v4
            - name: Use Node.js ${{ matrix.node-version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.node-version }}
                cache: 'npm'
            - run: npm ci
            - run: npm run build --if-present
        ```

       

        This GitHub Actions configuration sets up a CI/CD pipeline for a Node.js application. The workflow triggers on a push to the `main` branch. It runs on a `self-hosted` runner and uses Node.js version `18.x`. The steps include checking out the code, setting up Node.js, installing dependencies using `npm ci`, buildding the app.


### Deploy and Verify
1. **Push Changes to GitHub**: Commit and push changes to your GitHub repository.
2. **Check GitHub Actions**: Ensure the workflow runs successfully and deploys the updated code to your EC2 instance.

    ![alt text](https://github.com/Minhaz00/NodeJS-Tasks/blob/main/14.%20Deploy%20Express%20App%20in%20EC2%20Using%20Github%C2%A0Action/images/image-10.png?raw=true)

    If you go to the SSH terminal, you can see `_work` directory. Now go to `_work` folder. In this folder you will see your react app directory from github. Therefore code updated to your EC2 instance.

## Install Node.js and Nginx on EC2

Now go to `_work` folder. In this folder you will see your react app directory from github. Navigate to that directory. Use `ls` to see you app files.

1. **Update Packages**:
   ```bash
   sudo apt-get update
   ```

3. **Install Nginx**:
   ```bash
   sudo apt-get install -y nginx
   ```

    Now go to your instance, open the public IPv4 DNS link:

    ![alt text](https://github.com/Minhaz00/React-App-in-EC2/blob/main/image/image.png?raw=true)

    You will see Nginx home page.

    

##  Configure Nginx

1. **Copy the build path of your React App**:
    
    Go to your build directory and run the following command to get the directory path:
    ```bash
    pwd
    ```

2. **Edit Nginx Config**:

   ```bash
   cd /etc/nginx/sites-available
   sudo nano default
   ```

3. **Add Reverse Proxy Configuration**: Set the `server` block as follows.



    ```nginx
    server {
      listen 80 default_server;
      server_name _;

      react app & front-end files
      location / {
      root <react-app-build-dir-path>;
      try_files $uri /index.html;
      }
    }
    ```

    Replace `<react-app-build-dir-path>` with your apps build directory path.

4. **Restart Nginx**:
   ```bash
   sudo systemctl restart nginx
   ```


    Now go to your instance, open the public IPv4 DNS link. You will see bad gateway error. We need to set permission for the build directory. In our case, we ran `chmod +x` for every directory in path to build directory.

    ```bash
    sudo chmod +x /home
    sudo chmod +x /home/ubuntu/
    sudo chmod +x /home/ubuntu/actions-runner/
    sudo chmod +x /home/ubuntu/actions-runner/_work/
    sudo chmod +x /home/ubuntu/actions-runner/_work/React-App-in-EC2/
    sudo chmod +x /home/ubuntu/actions-runner/_work/React-App-in-EC2/React-App-in-EC2/
    sudo chmod +x /home/ubuntu/actions-runner/_work/React-App-in-EC2/React-App-in-EC2/build/
    ```

     Now go to your instance, open the public IPv4 DNS link to see your react app from ec2 instance:

     ![alt text](https://github.com/Minhaz00/React-App-in-EC2/blob/main/image/image-1.png?raw=true)
 

## Conclusion

In this lab, you automated the deployment of a React app to an EC2 instance using GitHub Actions. You established a CI/CD pipeline, ensuring your application updates automatically with each code push. This setup streamlines your workflow, providing faster, consistent, and reliable deployments.