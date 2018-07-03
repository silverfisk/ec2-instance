# ec2-instance

Ansible role to create AWS EC2 instances.

It adds allrunning instances with a matching Name & CostCenter tag to the inventory group ec2_pets. It also registers some variables such as volume and package installation information so it's available inside the instance during the play.


Example usage
-------------

vars/myvars.yaml:
 ```yaml
EnvironmentClass: "dev"
CostCenter: "12345"
Application: "cylon_laser"
OwnerEmail: "helpful.robot@scania.com"
ApplicationTier: "app"
PrivateSubnet1B: subnet-12345678      # prod
PrivateSubnet1C: subnet-12345679      # prod
vpc_id: vpc-12345678                  # prod
AWSRegion: eu-west-1

ec2_pets:
  group-apptier01-env:               # This will be the server name tag
    description: "App Tier server for {{ Application  }}"
    ssh_key: "my-ssh-key-dev"
    instance_type: t2.micro
    instance_ami: ami-e365fd9a
    limits:                         # Add limits.conf / limits.d
        limits_group:
          domain: "@groupname"
          limit_item: "nofile"
          limit_type: "-"
          value: "65536"
          dest: "/etc/security/limits.d/99-application.conf"
    security_groups: ['groupname_appname_env_sg']
    volumes:
      - device_name: /dev/xvdb
        volume_type: gp2
        volume_size: 20
        mount_point: /usr/sap
        fstype: xfs
        encrypt: "yes"
        resizefs: yes
    monitoring: "yes"
    termination_protection: "no"
    wait: "yes"
    vpc_subnet_id: "{{ PrivateSubnet1A }}"
    yum_packages:
      - sudo
      - "@base"                   # Package group
```