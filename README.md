aws_route53
=========

Take a aws_route53 dict of route53 attributes and create the DNS entry.

Requirements
------------


Role Variables
--------------

`stage` - default is create. Can have the value `delete`. This tells the role
 what to do.

`aws_route53_profile_account` - Optional - default: xvt_aws
 - the aws profile account name defined in the `aws` dict. This is to lookup
   the aws account id which is used to create the route53 entry.

`aws_route53_profile_account_role_arn` - Optional - default: "arn:aws:iam::{{
   aws[aws_route53_profile_account].id }}:role/route53_admin"
 - The IAM role in the aws account that has the route53 admin right. We will
 assume that role if possible.

`aws_route53_profile_account_region` - Optional - default: "{{
   aws[aws_route53_profile_account].region|default(region) }}"

`aws_route53_profile` - optional - no default
 - This is the aws profile (defined in ~/.aws/credential) for the aws account
   which is used to create the route53 entry. If not provided then we will
   assume the role using these above variables.

`aws_route53` - required - no default
 - A dict which has the key that the ansible module 'route53' can understand.
   We will pass these value to the origin ansible route53 module.
   The key name is exactly the ansible module 'route53' parameter name.
   compulsory keys listed in the example below

```
aws_route53:
  zone: "route_53_zone_to_operate"
  record: "the_record_to_operate"
  value: "value_if_stage_is_create"
  profile: "aws profile if not provided by any other means such as aws profile account or route53 iam assume role"

```

To authenticate you must provide one of
- aws_route53_profile_account
  - if provided then need to provide the `aws` dict describe above and
  - aws_profile_account_role_arn

- aws_route53_profile


`aws` - A dict contains aws account information for lookup
  sample:

```
  aws:
    xvt_aws:
      id: "XXX"
      key_pair:
        default: TBA
      sandbox_images:
      bucket_shortname: TBA
      envs:
      region:
      shortname:
      central_number:
```

Dependencies
------------

role: aws_profile_account
      to handle the case when we are running in ec2 which has the IAM profile
      that can assume the target route53_admin IAM role in the aws account that
      we create the entry.
      This eliminates the need to use the local target aws account profile.

Example Playbook
----------------


```
- hosts: servers
  tasks:
    - include_role:
        name: aws_route53
      vars:
        aws_route53_profile: xvt_sandbox
        stage: delete
        aws_route53:
          zone: "sbd.aws.planningportal.internal"
          record: "stevek-test.sbd.aws.planningportal.internal"
          value: "1.1.1.1"
          private_zone: yes

```

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
