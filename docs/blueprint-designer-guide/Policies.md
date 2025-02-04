---
sidebar_position: 16
title: Blueprint Policies
---

As the blueprint designer, you can define the blueprint's default and max durations or allow it to run indefinitely, to support scenarios that deploy an environment that must be always online or delivers a static cloud resource.

**To set the blueprint's policy:** 

1. In the **Blueprints** page, click the blueprint's more actions menu and select **Manage Policies**. 
2. In the dialog box, set the default durations as appropriate:
    * __Max Duration__: Set the max duration or select **Unlimited** to allow the environment end user to run the environment indefinitely.Note that end-users will still have the option to end or extend the environment at any time, regardless of its runtime policy.
    * __Default Duration__: Set the default duration that is used when launching a new environment.
    * __Default Extend Duration__: Set the default duration when end-users extend the environment.
3. Click __Apply__.