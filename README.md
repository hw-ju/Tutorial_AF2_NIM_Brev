# Tutorial_AF2_NIM_Brev
A step-by-step tutorial for deploying and using NVIDIA AlphaFold2 NIM on NVIDIA Brev. <br>

**NOTE**: The first time you run the AF2 NIM container, the APIs will take approximately 3–4 hours to become ready due to the downloading of large databases exceeding 1TB. <br>

This tutorial mainly combines instructions from [Deploying NIMs on Brev](https://docs.nvidia.com/brev/latest/deploying-nims.html) section of the Brev doc and [Quickstart Guide](https://docs.nvidia.com/nim/bionemo/alphafold2/latest/quickstart-guide.html) section of AF2 NIM doc. For more details, please refer to those docs. It is also highly recommended to go through the rest of Brev doc and AF2 NIM doc.

### Creat Brev Account
Create an account on Brev if you haven't already. You can do this by clicking the “Create an account” button in the top right corner of the [Brev Console](https://console.brev.dev/org/org-2tcmRZuGMBx9Wlh2bxmuIdGb6UN/environments).

### Install Brev CLI
- Follow [Installation Instructions](https://docs.nvidia.com/brev/latest/brev-cli.html#installation-instructions) for Mac, Linux and Windows.
    - Quick setup for Windows. If you don't have WSL2 installed on your Window now, just following this section [Install WSL command](https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command) should be enough.
- You are encouraged to familiarize yourself with the frequently used Brev CLI commands below, since we'll use them in the next steps. Please refer to the [Brev CLI doc](https://docs.nvidia.com/brev/latest/brev-cli.html#) for details.
    ```
    brev login
    ```
    ```
    brev refresh
    ```
    ```
    brev ls
    ``` 

### Create Launchable & Deploy Instance
- A "Launchable" is a template of setting up an instance.
- Open a web browser and go to [Brev Console](https://console.brev.dev/org/org-2tcmRZuGMBx9Wlh2bxmuIdGb6UN/environments).
    - Create an account on Brev if you haven't already. You can do this by clicking the “Create an account” button in the top right corner of the Console.
    - Click "Launchables" tab in the top menu bar. <br>
    <img src="images/tab_launchable.png" alt="Alt text" width="500"> <br>
    - Click "Create Launchables". <br>
    <img src="images/create_launchable.png" alt="Alt text" width="500"> <br>
    - Click "compute" -> "A100" -> "80GiB GPU Memory | 1 GPU • 170GiB RAM • 12 CPUs | GCP • $7.44/hr". <br>
    <img src="images/config_compute.png" alt="Alt text" width="500"> <br>
    - Type *1500* under "Storage (GiB)", then click "Save Compute". <br>
    <img src="images/config_storage.png" alt="Alt text" width="500"> <br>
    - Click "Container" -> Toggle the "Prestall Jupyter" switch to turn it on -> Click "VM Mode" box -> "Save Container". <br>
    <img src="images/config_container.png" alt="Alt text" width="500"> <br>
    - Click "Files" -> Copy & paste the link to the tutorial Jupyter Notebook for calling AF2 NIM APIs - [call_API.ipynb](call_API.ipynb) -> Click "Add file". <br>
    <img src="images/config_files.png" alt="Alt text" width="500"> <br>
    - Type under "Name Launchable", e.g., *AF2-NIM* -> Click "Generate Launchable". <br> 
    <img src="images/name_gen_launchable.png" alt="Alt text" width="500"> <br>
    - Click "View Launchable". <br>
    **Note that now you can copy & paste the link to this Launchable to share it with your friends, including all configurations such as compute, environment, and Jupyter Notebook, to make your work so much more reproducible.** <br>
    <img src="images/view_launchable.png" alt="Alt text" width="500"> <br>
    - Click "Deploy Launchable". <br>
    <img src="images/deploy_launchable.png" alt="Alt text" width="500"> <br>
    - Click "Go to Instance Page" when it becomes available and starts flashing. <br>
    <img src="images/go_to_instance_page.png" alt="Alt text" width="500"> <br>
    - You should now be on the instance page, as shown below. <br>
        - It might take a few minutes before you see the 'Running' status and button "Open Notebook" starts flashing. <br>
    <img src="images/instance_page_1.png" alt="Alt text" width="500">

### Required Utilities
In your local terminal where you installed Brev CLI:
- Log in to your Brev account by following the prompts.
    ```
    brev login
    ```
    <img src="images/brev_login.png" alt="Alt text" width="500">

- Print all your Brev instances. When the "STATUS" shows "RUNNING", proceed to the next step.
    ```
    brev ls
    ```
    <img src="images/brev_ls.png" alt="Alt text" width="500">

- SSH into your instance. 
    - It might take a while for it to become ready. 
    - When it's ready, you should see something like the screenshot below. Note that the prompt has changed.
    ```
    brev shell <instance-name>
    ```
    <img src="images/brev_shell.png" alt="Alt text" width="500">

- Verify your container runtime supports NVIDIA GPUs.
    ```
    docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
    ```
    <img src="images/verify_container_runtime.png" alt="Alt text" width="500">

- Generate NGC API Key.
    - Open a web browser and go to https://org.ngc.nvidia.com/setup/api-key.
    - You might be prompted to login or to register for a free account.
    - Click "+ Generate API Key" and then copy & paste the key to somewhere local. <br>
    <img src="images/gen_API_Key.png" alt="Alt text" width="500"> 

- Set up NGC CLI by following the section [NGC CLI Tool](https://docs.nvidia.com/nim/bionemo/alphafold2/latest/prerequisites.html#ngc-cli-tool) in the AF2 NIM doc. Please refer to this doc for explanation. I have put specific commands below:
    ```
    sudo apt install unzip
    ```


    ```
    wget --content-disposition https://api.ngc.nvidia.com/v2/resources/nvidia/ngc-apps/ngc_cli/versions/3.41.3/files/ngccli_linux.zip -O ~/ngccli_linux.zip && \
    unzip ~/ngccli_linux.zip -d ~/ngc && \
    chmod u+x ~/ngc/ngc-cli/ngc && \
    echo "export PATH=\"\$PATH:~/ngc/ngc-cli\"" >> ~/.bash_profile && source ~/.bash_profile
    ```

    Follow the prompts of the command below. Note that you need to copy & paste your NGC API Key at the first prompt.
    ```
    ngc config set 
    ```   
    <img src="images/ngc_config_set.png" alt="Alt text" width="500"> <br>

    
    For the command below, 
    - copy & paste "$oauthtoken" for the "Username" prompt.
    - copy & paste your NGC API Key at the "Password" prompt.
    ```
    docker login nvcr.io
    ```
    <img src="images/docker_login_nvcr.png" alt="Alt text" width="500"> <br>

    List available NIMs, e.g., the AI models originally developed by deepmind:
    ```
    ngc registry image list --format_type csv nvcr.io/nim/deepmind/*
    ```
    <img src="images/list_avail_nims.png" alt="Alt text" width="500">

### Deploy AF2 NIM
- Set up the NIM cache
    - Create the NIM cache directory
        ```
        mkdir -p ~/.cache/nim
        ```
        <img src="images/mkdir_cache.png" alt="Alt text" width="500">
    - Set the NIM cache directory permissions to the correct values
        ```
        chmod -R 777 ~/.cache/nim
        ```
        <img src="images/chmod_cache.png" alt="Alt text" width="500">

- Pull the AF2 NIM container. Might take about five minutes.
    ```
    docker pull nvcr.io/nim/deepmind/alphafold2:2.1.0
    ```
    <img src="images/docker_pull.png" alt="Alt text" width="500">
    When it's done, you should see:
    <img src="images/docker_pull_done.png" alt="Alt text" width="500">

- Set the two environmental variables below one-by-one, which will be passed into the container in the next step.
    ```
    export LOCAL_NIM_CACHE=~/.cache/nim
    export NGC_API_KEY=<Your NGC API KEY>
    ```
    <img src="images/export_env_var.png" alt="Alt text" width="500">

- `docker run` the NIM container for the **first time**. 
    - Why use `nohup`: prevents the process from stopping when the terminal session is closed or disconnected.
    ```
    nohup docker run --rm --name alphafold2 --runtime=nvidia \
    -e NGC_API_KEY \
    -v $LOCAL_NIM_CACHE:/opt/nim/.cache \
    -p 8000:8000 \
    nvcr.io/nim/deepmind/alphafold2:2.1.0  > af2_nim.log 2>&1 &
    ```
    <img src="images/nohup.png" alt="Alt text" width="500">

- Monitor the log of the running NIM container.
    ```
    tail -f af2_nim.log
    ```
    <img src="images/first_run_log.png" alt="Alt text" width="500"> <br>

    You can quit monitoring the log anytime by pressing `Ctrl + C`. <br>
    To re-monitor the log, run the tail command above again.

    - The first time you run the AF2 NIM container, the log will hang at the status shown in the screenshot above for a long time. The APIs will take approximately 3–4 hours to become ready due to the downloading of large databases exceeding 1TB. 
    - The screenshot below indicates when the APIs are ready. <br>
    <img src="images/nim_ready.png" alt="Alt text" width="500">

- Delete the NIM container when you're done with calling the NIM APIs.
    - Get the ID of the running NIM container <br>
    ```
    docker ps
    ```
    <img src="images/docker_ps.png" alt="Alt text" width="500"> <br>

    - Kill the running NIM contaienr <br>
    ```
    docker kill <container_ID>
    ```
    <img src="images/docker_kill.png" alt="Alt text" width="500"> <br>

- `docker run` the NIM container **after the first run**.
    After the first run, the model weights are already downloaded. Pass `NIM_DISABLE_MODEL_DOWNLOAD=True` as an environment variable to the container to speed up the startup (almost instant startup).
    ```
    export NIM_DISABLE_MODEL_DOWNLOAD=True
    ```
    ```    
    nohup docker run --rm --name alphafold2 --runtime=nvidia \
        -e NIM_DISABLE_MODEL_DOWNLOAD \
        -e NGC_API_KEY \
        -v $LOCAL_NIM_CACHE:/opt/nim/.cache \
        -p 8000:8000 \
        nvcr.io/nim/deepmind/alphafold2:2.1.0  > af2_nim.log 2>&1 &
    ```

### Run Tutorial Jupyter Notebook - call AF2 NIM APIs
- Click on the flashing "Open Notebook" button. <br>
<img src="images/click_open_notebook.png" alt="Alt text" width="500"> <br>
- You should now be directed to a Jupyter Lab that appears in your web browser. If it doesn’t load, try refreshing the page. <br>
Close up the benign error if it pops up. <br>
<img src="images/path_not_found.png" alt="Alt text" width="300"> <br>
Double click the tutorial Jupyter Notebook `call_API.ipynb` in the left panel to open it up. <br>
<img src="images/click_ipynb.png" alt="Alt text" width="500"> <br>
- You're ready to run the tutorial Jupyter Notebook. <br>
<img src="images/ready.png" alt="Alt text" width="500">


