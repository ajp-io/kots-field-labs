Multi Player Mode
======================


This guide will walk you through running all the labs for a team of people.

## 1. Prerequisites


+ You should probably skim through [the architecture outline](./01-architecture.md) first.
+ You should be able to setup the labs in [single player mode](./02-single-player.md).
+ `invite_rbac_policy_id` obtained from vendor.replicated.com
  + Navigate to Team > RBAC > Admin > View Policy, and pull the policy ID from the URL bar or the developer console.
  > Example: `https://vendor.replicated.com/team/policy/HPCzGS0cRaKvV7fTJRCSL02K_f5V0aBn`

## 2. Initialization

When provisioning for an actual training, the following is needed (to invite users):

```(shell)
make apps invite_users=1 env_csv=[CSV File with ssh  public keys] labs_json=labs_all.json prefix=[PREFIX] inviter_password=[Your Vendor web password] invite_rbac_policy_id=[Check the RBAC token in chrome dev tools] inviter_email=[your Vendor web email]
```

A csv example:
```csv
Timestamp,participant Email,name,pub_key,password,email,slug
5/13/2021 18:02:25,participant.one@somecompany.com,Participant One,ssh-rsa public key particpant.one@somecompany.com,password,participant.one+[PREFIX]@somecompany.com,participant
```

In above example, the following fields are being mapped:
+ `participant Email`: E-mail address of participant
+ `name`: name of the participant
+ `pub_key`: public key of the user who will access this environment
+ `password`: password to be set on kotsadm instances
+ `email`: email to invite to vendor.replicated.com if Params.InviteUsers is set
+ `slug`: slug of the environment




## 3. VM Instances

See [Terraform apply](./02-single-player.md#6-terraform-apply)
```shell
make instances
```

## 4. Tearing it down

After the training is done, we usually give them a couple days to finish the labs.
1. End of training day 2: Ask who is finished with the labs and remove their instances
    ```shell
    gcloud compute instances delete $(gcloud compute instances list --filter="name:[PREFIX]" | awk '{ print $1 }' | grep [USERNAME]) --zone us-central1-b
    ```
1. Day after the training: Ask again who is finished with the labs and remove their instances.
1. 3 days after the training was done: Let them know all instances will be removed EOD, unless someone lets us know they will need more time. See [cleaning up](./02-single-player.md#10-cleaning-up) 
