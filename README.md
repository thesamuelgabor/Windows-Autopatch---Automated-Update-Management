# Windows Autopatch - Automated Update Management

## Objective

This project configures Windows Autopatch to automate Windows quality updates, feature updates, driver updates, Microsoft 365 Apps updates and Microsoft Edge updates for the devices provisioned in [Microsoft Entra Joined/Cloud-Only Windows-11 Autopilot](https://github.com/thesamuelgabor/Microsoft-Entra-Joined-Cloud-Only-Windows-11-Autopilot) and [Windows Autopilot Device Preparation](https://github.com/thesamuelgabor/Windows-Autopilot-Device-Preparation-New-Experience), replacing manually managed update policies with Microsoft-managed, staged deployment rings.

### Skills Learned

- Installing the Windows Autopatch client broker
- Creating an Autopatch group with dynamic group distribution
- Deployment ring design (Test, Ring1, Ring2, Ring3, Last)
- Choosing update types, a feature update target version and a release schedule
- Software update policy behaviour once managed by Autopatch
- Reading the Autopatch device health reports

### Tools Used

- Windows Autopatch (Intune admin center → Tenant administration)
- Microsoft Entra ID dynamic groups

## Steps

#### 1. Install the Client Broker

In the Intune admin center, went to **Tenant administration → Windows Autopatch → Tenant management** and selected **Manage client broker → Install**.

The client broker is the agent that lets Autopatch-registered devices send update readiness data and log collection information back to the Autopatch service.

<img width="961" height="289" alt="image" src="https://github.com/user-attachments/assets/ace7decf-be1c-4706-a73c-e63be54b0e27" />

*Ref 1: Installing the client broker*

#### 2. Create the Autopatch Group

Created an Autopatch group named `Win11-Autopatch` and configured it as follows.

**Deployment rings and distribution**

- Set **dynamic group distribution** to `DG-Win11-Autopilot-Pilot`, the dynamic group from the Autopilot project.
- Added three extra rings, giving five in total: **Test**, **Ring1**, **Ring2**, **Ring3** and **Last**.
- Set the distribution percentages for the middle rings (10 / 25 / 65 %). Autopatch then places devices into the rings automatically.

**Update types**

| Update type | Setting |
|---|---|
| Quality updates | Enabled, deployed automatically through the ring schedule |
| Feature updates | Enabled, target version **Windows 11, version 26H2** |
| Driver updates | Enabled, all drivers approved automatically |
| Microsoft 365 Apps updates | Enabled |
| Microsoft Edge updates | Enabled, **Stable** channel |

**Release schedule:** selected the **Information worker** preset.

With the feature update target set to 26H2, any device on an older version upgrades as soon as it checks in. Devices already on the target version stay where they are.

#### 3. Deployment Rings

The finished `Win11-Autopatch` group:

| Deployment ring | Assigned group | Dynamic group distribution | Approx. device count |
|---|---|---|---|
| Win11-Autopatch - Test | None | Not applicable | 0 |
| Win11-Autopatch - Ring1 | None | 10 % | about 1 |
| Win11-Autopatch - Ring2 | None | 25 % | about 1 |
| Win11-Autopatch - Ring3 | None | 65 % | about 1 |
| Win11-Autopatch - Last | None | Not applicable | 0 |

**Test** and **Last** are not part of the dynamic distribution. Devices only land in them through an assigned group, so they stay empty until a group is assigned: Test for IT validation devices, Last for sensitive devices that should update after everyone else.

<img width="1387" height="869" alt="Win11-Autopatch group showing deployment rings, update types and deployment settings" src="https://github.com/user-attachments/assets/25aa7fe1-1dab-4307-a7d5-1e724a953895" />

*Ref 3: Deployment rings and update types*

#### 4. Review Auto-Generated Update Policies

Confirmed Autopatch automatically created and scoped the Windows quality update, feature update and driver update policies for each ring, so no manually created update policies were needed going forward.

<img width="1372" height="272" alt="image" src="https://github.com/user-attachments/assets/5fbe941f-2423-4f0d-85c2-09cd469d262d" />

*Ref 4: Update policies*

#### 5. Monitor Device Health

Used the Autopatch device health report to track update compliance and flagged devices, and drilled into one flagged device to identify the blocking issue.

<img width="1607" height="509" alt="image" src="https://github.com/user-attachments/assets/4ad63c24-e765-422b-a6a8-40a1d658a1fc" />

*Ref 5: Device health monitoring*
