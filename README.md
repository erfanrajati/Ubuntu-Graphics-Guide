# Display and Graphics on Ubuntu: A Comprehensive Guide

This guide covers essential concepts, tools, and troubleshooting steps for managing display and graphics settings on Ubuntu, with a focus on NVIDIA GPU management, display server protocols (Wayland vs Xorg), hardware monitoring, and key commands for system resource management.

## 1. **Wayland vs Xorg**
### Overview:
- **Xorg**: The traditional display server used in Linux systems. It has been the default for many years, offering compatibility with a wide range of hardware and applications.
- **Wayland**: A newer display server protocol designed to eventually replace Xorg. It is built to provide improved security, better performance, and smoother graphics, but some applications and hardware (such as NVIDIA GPUs) may face compatibility issues or require additional configuration.

### Switching Between Wayland and Xorg:
- **To switch to Xorg**: 
  - At the login screen, click the gear icon and select "Ubuntu on Xorg."
- **To switch back to Wayland**: 
  - At the login screen, select "Ubuntu" to use Wayland.
  
Switching between these two servers is useful for resolving issues like external displays not being detected or GPU performance problems.

---

## 2. **NVIDIA Drivers**

### Installing NVIDIA Drivers:
To install the NVIDIA proprietary drivers on Ubuntu, run the following command:
```bash
sudo apt install nvidia-driver
```
This will automatically install the appropriate driver for your GPU.

### Managing NVIDIA GPU:
You can use the `nvidia-smi` tool to check the status of your NVIDIA GPU, including information like GPU utilization, memory usage, and temperature.

### Switching Between Integrated and Discrete GPUs:
For laptops with hybrid graphics (integrated Intel and discrete NVIDIA GPUs), use the `prime-select` command to switch between the two GPUs:

- To switch to the NVIDIA GPU:
  ```bash
  sudo prime-select nvidia
  ```
- To switch to the integrated Intel GPU:
  ```bash
  sudo prime-select intel
  ```

### Blacklisting the `nouveau` Driver:
The open-source `nouveau` driver can sometimes conflict with the official NVIDIA driver. To prevent this, you can blacklist it:
1. Create a new configuration file:
   ```bash
   sudo nano /etc/modprobe.d/blacklist-nouveau.conf
   ```
2. Add the following lines:
   ```
   blacklist nouveau
   options nouveau modeset=0
   ```
3. Update the initial ramdisk:
   ```bash
   sudo update-initramfs -u
   ```

---

## 3. **xrandr Commands**

### Basic Usage:
`xrandr` is a command-line tool for managing display settings, including screen resolution and orientation.

- **List connected monitors**:
  ```bash
  xrandr --listmonitors
  ```
- **Automatically configure a connected display**:
  ```bash
  xrandr --output <display_name> --auto
  ```
- **Change the resolution of a display**:
  ```bash
  xrandr --output <display_name> --mode <resolution>
  ```
- **Rotate the display**:
  ```bash
  xrandr --output <display_name> --rotate <left|right|normal>
  ```
- **Set a display as primary**:
  ```bash
  xrandr --output <display_name> --primary
  ```

---

## 4. **Display Settings and Troubleshooting**

### Switching Between Xorg and Wayland:
As explained earlier, switching between Xorg and Wayland can help resolve issues related to external displays and GPU performance. 

### Troubleshooting Display Issues:
- **External displays not detected**: This could be caused by a conflict between Xorg and Wayland or an issue with GPU settings. Ensure the correct display server is selected and that the GPU is properly activated.
- **Armoury Crate and External Displays**: On ASUS laptops, Armoury Crate software can disable the GPU completely in Windows, causing external displays to be undetected in Ubuntu. Switch Armoury Crate to "standard" mode in Windows to resolve this.

---

## 5. **Power Management and Hybrid Graphics**

Ubuntu supports hybrid graphics setups, allowing users to switch between the integrated Intel GPU for power efficiency and the discrete NVIDIA GPU for higher performance. The **Optimus** technology is commonly used for automatic GPU switching. 

Use `prime-select` for manual control over GPU selection:
- Switch to **NVIDIA GPU** for performance:
  ```bash
  sudo prime-select nvidia
  ```
- Switch to **Intel GPU** for power-saving:
  ```bash
  sudo prime-select intel
  ```

---

## 6. **Hardware Sensors and Monitoring**

### **libsensors**:
The **libsensors** library provides access to hardware sensors like CPU temperature, fan speed, and system voltages. The data from these sensors can be read using the `sensors` command.

- **Check system temperature and fan speed**:
  ```bash
  sensors
  ```

### Software Monitoring Commands:
To monitor system resources such as CPU usage, memory usage, and running processes, the following tools can be used:

#### **htop**:
`htop` is an advanced interactive system monitor that provides real-time monitoring of system resources.
- **Installation**:
  ```bash
  sudo apt install htop
  ```
- **Usage**:
  Run `htop` in the terminal to get a detailed overview of CPU, memory, and process usage. Use the function keys to interact with processes (e.g., `F9` to kill a process).

#### **ps**:
The `ps` command is used to display information about active processes.
- **List all processes**:
  ```bash
  ps aux
  ```
- **List processes in a parent-child hierarchy**:
  ```bash
  ps -ef
  ```
- **Search for a specific process**:
  ```bash
  ps aux | grep <process_name>
  ```

## 7. A General Guide to Using TLP for Power Management on Ubuntu

TLP is a powerful tool designed to optimize power management on Linux systems. Here's a brief guide to getting started with TLP, configuring it, and understanding its capabilities:

---

#### 1. **Installing TLP**
TLP can be installed from the default Ubuntu repositories:
```bash
sudo apt install tlp
```
To ensure compatibility with your hardware, also install any vendor-specific drivers or add-ons:
```bash
sudo apt install tlp-rdw
```

#### 2. **Starting TLP**
Once installed, TLP is enabled by default. You can start it manually if needed:
```bash
sudo tlp start
```

---

#### 3. **Configuring TLP**
The configuration file for TLP is located at `/etc/tlp.conf`. Edit this file to customize power management settings:
```bash
sudo nano /etc/tlp.conf
```

Key options to configure:
- **Charge thresholds** for battery health:
  ```bash
  START_CHARGE_THRESH_BAT0=40
  STOP_CHARGE_THRESH_BAT0=80
  ```
  This ensures the battery charges only between 40% and 80%.

- Save and apply changes by restarting TLP:
  ```bash
  sudo tlp start
  ```

---

#### 4. **Checking TLP Status**
To view the current status of TLP and the system's power management settings:
```bash
sudo tlp-stat
```
Key sections in the output:
- **Driver usage**: Confirms if charge threshold drivers (e.g., `natacpi`) are active.
- **Battery status**: Displays charge levels, capacity, and thresholds.
- **CPU settings**: Shows power-saving states for the CPU.

---

#### 5. **Charge Thresholds**
Charge thresholds are particularly useful for maintaining battery health. Ensure your hardware supports this feature by checking the **Driver usage** section in `tlp-stat`.

Example:
```bash
/sys/class/power_supply/BAT0/charge_control_end_threshold = 80 [%]
```
This means the battery will stop charging when it reaches 80%.

---

#### 6. **Important Commands**
- **Check battery status**:
  ```bash
  sudo tlp-stat -b
  ```
- **View power settings**:
  ```bash
  sudo tlp-stat -c
  ```
- **Restart TLP after changes**:
  ```bash
  sudo tlp start
  ```

---

#### 7. **Things to Keep in Mind**
- **Hardware Compatibility**: Not all laptops support advanced TLP features like charge thresholds. Check the **Driver usage** section in `tlp-stat` for compatibility.
- **Battery Health**: Use charge thresholds (e.g., 40–80%) to prolong battery life.
- **Custom Configurations**: Avoid unnecessary edits to `tlp.conf`. Use the default settings unless specific tweaks are required.

---

With TLP properly configured, you can effectively manage your system's power usage and extend your laptop's battery life.

