# Cisco Password Recovery Lab Packet Tracer

Can you believe it?

The previous network administrator was fired, and he was the only person who knew the passwords for these devices.

To make matters worse, there is no documentation containing the passwords. Your task is to troubleshoot the network and recover access to **two routers and one switch**.

This lab covers password recovery on Cisco 4000 Series Integrated Services Routers and a Catalyst 3560 switch.

In the real world, you can't remember everything, so it's very important to learn how to use Google and Cisco's official documentation to help you.

![Network topology](topology.png)

For example, on **R1**, the router boots normally, but when I type `enable` and enter a password, it doesn't work.

![R1 enable password issue](screenshots/r1.1.png)

I can't reload the router because I don't have privileged EXEC access. I can't do much except use some basic `show` commands.

This is a problem. and it's your responsibility to fix it.

## Lab Objectives

1. **R1:** Remove the old enable password and replace it with an enable secret set to `cisco`.
2. **R2:** Reset the enable secret to `cisco`.
3. **S1:** Reset the enable secret to `cisco`.
4. Verify that the original configurations are restored and that all devices can ping all loopback addresses.
5. Power cycle all devices and verify that their configurations are preserved and the new passwords work.

**[Download the Packet Tracer lab](Password%20Recovery%20Lab.pkt)**

## Cisco 4000 Series Router Password Recovery

The router password recovery process in Packet Tracer is very similar to the process on real Cisco routers.

1. **Reboot the router.** During startup, send a break sequence (such as `Ctrl+C` or `Ctrl+Break`) to enter **ROM Monitor (ROMmon)** mode.

2. **Change the configuration register to `0x2142`.** This tells the router to ignore its saved startup configuration during the next boot and start with a default configuration..

   ![Changing the configuration register](screenshots/r1.2.png)

   To understand the configuration register, read [Cisco's configuration register guide](https://www.cisco.com/c/en/us/support/docs/routers/10000-series-routers/50421-config-register-use.html).

3. After the router restarts, it boots with a default running configuration. Enter privileged EXEC mode using `enable`.

4. Use `show startup-config` to inspect the previously saved configuration, including the old enable password settings.

   ![Viewing the startup configuration](screenshots/r1.3.1.png)

   Then restore the saved configuration to RAM:

   ![Restoring the configuration](screenshots/r1.3.png)

5. **On R1**, remove the old enable password and replace it with an enable secret set to `cisco`. **On R2**, reset the enable secret to `cisco`.

   ![Setting the new enable secret](screenshots/r1.4.png)

6. Restore the default configuration register (`0x2102`), save the configuration, and reload the router.

After the reload, verify that you can access both routers using the new enable secret.

![Verifying password recovery](screenshots/r1.5.png)

## Cisco 3560 Switch Password Recovery

**Note:** Packet Tracer behaves a little differently from real hardware, but the switch password recovery process is similar.

1. **Power cycle the switch** using this icon in Packet Tracer:

   ![Power cycle icon](screenshots/icon.png)

   At the same time, press and hold the switch's **MODE button** to access the switch boot loader (`switch:` prompt).

   ![Switch boot loader](screenshots/s1rommon.png)

2. Once you are in the **switch boot loader**, initialize Flash and check the stored files:

   ![Initializing Flash](screenshots/s1.2.png)

3. **Check the flash files and Rename the configuration file** so the switch boots without loading the old password settings.

   Flash is non-volatile memory that stores files such as the IOS image and the switch's saved configuration (`config.text`).

   By renaming `config.text` to `config.text.old`, we preserve the original configuration while preventing the switch from loading it during startup. This allows us to bypass the old password without losing the original configuration.

   ![Renaming the configuration file](screenshots/s1.3.png)

4. **Delete the NVRAM startup configuration in Packet Tracer**, as shown below.

   ![Deleting NVRAM](screenshots/nvram.png)

   This is an additional step used in this Packet Tracer lab. On real Cisco switches, deleting the startup configuration is generally not necessary for password recovery.

5. After deleting the NVRAM startup configuration, the **initial configuration dialog** appears. Select **No**.

   ![Initial configuration dialog](screenshots/s1dialog.png)

   The switch has now booted with a default running configuration. Enter privileged EXEC mode using `enable`.

6. **Restore the original configuration from Flash into RAM:**

   This restores the previously saved configuration, without requiring the old enable password.

   ![Restoring the original configuration](screenshots/s1.4.png)

7. **Set the new enable secret to `cisco`**, then save the configuration:

   ![Setting the new enable secret](screenshots/s1.5.png)

8. **Reload the switch** and verify that you can access it using the new password and that the original configuration has been restored.

   ![Reloading the switch](screenshots/s1.6.png)

   ![Verifying password recovery](screenshots/s1.7.png)

**Password recovery was successful!**

## Official Cisco Documentation

- [Password Recovery for Cisco IOS and IOS XE Routers](https://www.cisco.com/c/en/us/support/docs/ios-nx-os-software/ios-xe-16/217045-troubleshoot-password-recovery-in-cisco.html)
- [Password Recovery for Catalyst Fixed-Configuration Switches](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-2950-series-switches/12040-pswdrec-2900xl.html)
- [Understanding Configuration Register Usage](https://www.cisco.com/c/en/us/support/docs/routers/10000-series-routers/50421-config-register-use.html)
## Cisco Documentation (PDF)

1. [Catalyst 3560 Software Configuration Guide](Catalyst%203560%20Software%20Configuration%20Guide%2C%20Release%2012.2%2852%29SE.pdf)
2. [Troubleshooting Guide for the Cisco 4000 Series Integrated Services Routers](Troubleshooting%20Guide%20for%20the%20Cisco%204000%20Series%20Integrated%20Services%20Router.pdf)


*The password `cisco` is used for this lab only. Use a strong, unique password on real devices.*
