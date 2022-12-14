## Table Of Contents

- [ Setting Default Value As A List ](#creating-list-from-dict-item--instance_id)
- [ Creating List from Dict Values ](#creating-list-from-dict-item--instance_id)
- [ Reboot Module](#reboot-module) 
   - https://earlruby.org/2019/07/rebooting-a-host-with-ansible/

- [ Creating List From Dict Values ](#creating-list-from-dict-values)


###  Creating List From Dict Item  instance_id 

```sh
    "asg": {
        "changed": false,
        "failed": false,
        "results": [
            {
                "auto_scaling_group_arn": "arn:aws:autoscalinautoScalingGroupName/shopping-asg",
                "auto_scaling_group_name": "shopping-asg",
                "availability_zones": [
                    "ap-south-1c",
                    "ap-south-1b",
                    "ap-south-1a"
                ],
                "created_time": "2022-12-08T14:06:21.657000+00:00",
                "default_cooldown": 300,
                "desired_capacity": 2,
                "enabled_metrics": [],
                "health_check_grace_period": 300,
                "health_check_type": "EC2",
                "instances": [
                    {
                        "availability_zone": "ap-south-1b",
                        "health_status": "Healthy",
                        "instance_id": "i-0443efcaee2773270",
                        "instance_type": "t2.micro",
                        "launch_configuration_name": "shopping-lc",
                        "lifecycle_state": "InService",
                        "protected_from_scale_in": false
                    },
                    {
                        "availability_zone": "ap-south-1a",
                        "health_status": "Healthy",
                        "instance_id": "i-0bb59fbfd45ddbb98",
                        "instance_type": "t2.micro",
                        "launch_configuration_name": "shopping-lc",
                        "lifecycle_state": "InService",
                        "protected_from_scale_in": false
                    }
                ],
                "launch_config_name": "shopping-lc",
                "launch_configuration_name": "shopping-lc",
                "lifecycle_hooks": [],
                "load_balancer_names": [],
                "max_size": 2,
                "min_size": 2,
                "new_instances_protected_from_scale_in": false,
                "service_linked_role_arn": "arn:aws:iam::ole/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling",
                "suspended_processes": [],
                "tags": [
                    {
                        "key": "Name",
                        "propagate_at_launch": true,
                        "resource_id": "shopping-asg",
                        "resource_type": "auto-scaling-group",
                        "value": "webserver"
                    }
                ],
                "target_group_arns": [],
                "target_group_names": [],
                "termination_policies": [
                    "Default"
                ],
                "vpc_zone_identifier": "subnet-09df26795d3779c11,subnet-0970b1a17c1cfd04d,subnet-0d580c3c810ce1d21"
            }
        ]
    }
}
```


#### Answer


```sh
    - name: "Append Instance Ids To List"
      set_fact:
        instance_ids: "{{ instance_ids | default([]) + [ item.instance_id ] }}"
      loop: "{{asg.results.0.instances}}"
```


### Reboot Module


```sh

- name: Reboot host and wait for it to restart
  reboot:
    msg: "Reboot initiated by Ansible"
    connect_timeout: 5
    reboot_timeout: 600
    pre_reboot_delay: 0
    post_reboot_delay: 30
    test_command: whoami
    
```


- Reboot the host
- Wait 30 seconds
- Attempt to connect via ssh and run whoami
- Disconnect after 5 seconds if it ssh isn???t working
- Keep attempting to connect for 10 minutes (600 seconds)


### Creating List From Dict values

```sh

    - name: "Creating A List Of Autoscaling Group Instances Ids"
      set_fact:
        instances: "{{ asg_info.results.0.instances | map(attribute='instance_id') }}"

    - debug:
       var: instances
```       
  
```sh

ok: [localhost] => {
    "instances": [
        "i-0443efcaee2773270",
        "i-0bb59fbfd45ddbb98"
    ]
}

```

```sh
    "asg_info": {
        "changed": false,
        "failed": false,
        "results": [
            {
                "auto_scaling_group_arn": "",
                "auto_scaling_group_name": "shopping-asg",
                "availability_zones": [
                    "ap-south-1c",
                    "ap-south-1b",
                    "ap-south-1a"
                ],
                "created_time": "2022-12-08T14:06:21.657000+00:00",
                "default_cooldown": 300,
                "desired_capacity": 2,
                "enabled_metrics": [],
                "health_check_grace_period": 300,
                "health_check_type": "EC2",
                "instances": [
                    {
                        "availability_zone": "ap-south-1b",
                        "health_status": "Healthy",
                        "instance_id": "i-0443efcaee2773270",
                        "instance_type": "t2.micro",
                        "launch_configuration_name": "shopping-lc",
                        "lifecycle_state": "InService",
                        "protected_from_scale_in": false
                    },
                    {
                        "availability_zone": "ap-south-1a",
                        "health_status": "Healthy",
                        "instance_id": "i-0bb59fbfd45ddbb98",
                        "instance_type": "t2.micro",
                        "launch_configuration_name": "shopping-lc",
                        "lifecycle_state": "InService",
                        "protected_from_scale_in": false
                    }
                ],

```
