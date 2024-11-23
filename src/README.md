# Source code
Let's install, build and run!

Contents:\
[Preliminary notes](#preliminary-notes)\
[Install](#install)\
[Build](#build)\
[Run](#run)



## Preliminary notes
1. We have tried to make the setup as user-friendly as possible. As a result, there is great overlap in the steps to take for running a simulation and a lab experiment. All the common instructions are written here, while the specific instructions for simulations and lab experiments are written in [sim.md](./sim.md) and [lab.md](./lab.md), respectively.

2. All software is tested on a machine running Ubuntu 20.04.6 LTS with ROS Noetic. The setup should work on other Ubuntu versions as well, but we cannot guarantee this. If you encounter any issues, please let us know by creating an issue in this repository.

3. Note that some links in this and other READMEs do not work on [github.com](https://github.com). To view the links, clone the repository including all its submodules and open the READMEs in your favourite editor.

4. Interested in using this setup for your own system? This is certainly possible! Our [MPC package](./catkin_ws/src/mpc) can easily be extended to other robotic platforms and tasks by defining different models, objectives, constraints, etc. Furthermore, it supports data logging, visualization, and real-time performance evaluation.

5. Are you using a PX4-powered quadrotor platform? Check out [our fork of PX4-Autopilot](./PX4-Autopilot)! We can communicate the hover thrust estimate via [our fork of mavros](./catkin_ws/src/mavros) to any ROS node. This is useful if you want to control the quadrotor with body rate or attitude commands.



## Install
### GitHub
We recommend setting your connection with GitHub up using SSH. See [this page](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) for more information.



### Docker
Install [Docker](https://docs.docker.com/) by following the instructions on [this page](https://docs.docker.com/get-started/get-docker). Give Docker sudo rights by following the instructions on [this page](https://docs.docker.com/engine/install/linux-postinstall/).



### ForcesPro license
The MPC implementations use a ForcesPro solver. Therefore, first request a ForcesPro license and install the client. To this end, follow the corresponding instructions in the [MPC README](./catkin_ws/src/mpc/README.md). Note that you need to have a ForcesPro floating license, since our setup runs in a Docker container.


### MATLAB and dependencies
For the [offline terminal ingredients design](./catkin_ws/src/mpc/mpc_solver/scripts/include/offline_computations/mpc-sdp) we use [MATLAB R2023a](https://nl.mathworks.com/products/new_products/release2023a.html) with the following packages:
- [CasADi](https://web.casadi.org/get)
- [YALMIP](https://yalmip.github.io) via [tbxmanager](https://tbxmanager.com)
- [SDPT3](https://blog.nus.edu.sg/mattohkc/softwares/sdpt3)

> :information_source: The terminal ingredients are already computed and included in this repository. These packages are only required if you want to change the offline design.



## Build
The build instruction differ between simulations and lab experiments. First execute the following common instructions:

1. Go to the `src` directory of this repo (important to prevent deleting the *.git* directory in your local repo clone):
    ```
    cd src
    ```

2. Build the Docker image by filling in the required arguments:
    ```
    sudo ./docker_build.sh
    ```
    Give the Docker image a descriptive name, such as *hmpc-sim* or *hmpc-lab*.

3. Create and run container from the built image by filling in the required arguments:
    ```
    ./docker_run.sh
    ```
    Give the Docker container a descriptive name, such as *dennis-paper-hmpc*. From now on, we refer to the container with this name.

4. Exit the container and source the following aliases in the *~/.bashrc* file on your host machine:
    ```
    alias cdmpcsolver='cd <path_to_repo>/src/catkin_ws/src/mpc/mpc_solver'
    alias mpc_generate_solver_hmpc='cdmpcsolver; ./setup_script.sh -c tmpc_settings.py -c pmpc_settings.py -s hovergames -f ARM'
    alias mpc_generate_solver_smpc='cdmpcsolver; ./setup_script.sh -c smpc_settings.py -s hovergames -f ARM'
    ```
    where `<path_to_repo>` is the path to this repository on your host machine.

5. After sourcing the aliases, generate the HMPC solver:
    ```
    mpc_generate_solver_hmpc
    ```
    or the SMPC solver:
    ```
    mpc_generate_solver_smpc
    ```
    In the [mpc_solver](./catkin_ws/src/mpc/mpc_solver) package, this will generate a directory *hovergames* in the [include](./catkin_ws/src/mpc/mpc_solver/include/mpc_solver/) and [src](./catkin_ws/src/mpc/mpc_solver/src) directories and a file *cmake_globalvars.cmake* in the [src](./catkin_ws/src/mpc/mpc_solver/src) directory.

6. Now, refer to [sim.md](./sim.md) and [lab.md](./lab.md) for the specific instructions to run simulations and lab experiments.

    In the Docker simulation and lab images, we have added a tmuxinator project that you can start using `shmpc`, attach to using `ahmpc` and kill using `khmpc`. This is the recommended way to work in the container and is assumed to be used in the remaining instructions. See the [tmuxinator](https://github.com/tmuxinator/tmuxinator) and [tmux](https://github.com/tmux/tmux/wiki) pages for more information.

    > :information_source: Compared to the default tmux settings, we have changed the prefix key to `Ctrl-a` for convenience of usage, see the [tmux.conf](./config_files/dotfiles/.tmux.conf) file for more information.



## Run
The run instructions differ between simulations and lab experiments. Refer to [sim.md](./sim.md) and [lab.md](./lab.md) for the specific instructions.
