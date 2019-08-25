Ansible AWS IAM management
=========

Ansible Role designed to create IAM users, groups and policies in AWS.

Requirements
------------

- Python Boto

Role Variables
--------------

- **iam_group_name**: IAM group name.
- **iam_policy_name**: IAM policy name.
- **iam_policy_json_path**: if we are defining a custom policy from a JSON file, we must set this variable with the path of the source JSON.
- **iam_managed_policy**: if we are attaching an AWS managed policy, this variables defines the name of the policy.
- **iam_username**: IAM username.
- **iam_user_tmp_pass**: temporal password that the use must use to login for the first time. The role enforces the change of this password in the first login.
- **iam_user_groups**: list of groups that the user is going to join

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```
- name: Create groups
  include_role:
    name: ansible-aws-iam
    tasks_from: "group.yml"
  vars:
    iam_group_name: "{{ project_name }}_developer"

- name: Create custom polices and attach to group
  include_role:
    name: ansible-aws-iam
    tasks_from: "policy.yml"
  vars:
    iam_group_name: "{{ project_name }}_developer"
    iam_policy_name: "{{ (item | basename | splitext).0 }}"
    iam_policy_json_path: "{{ item }}"
  with_fileglob:
    - "../files/aws_iam_policies/*"

- name: Attach AWS managed policies
  include_role:
    name: ansible-aws-iam
    tasks_from: "attach_managed_policy_to_group.yml"
  vars:
    iam_group_name: "{{ project_name }}_developer"
    iam_managed_policy: "{{ item }}"
  with_items:
    - CloudWatchReadOnlyAccess

- name: Create users
  include_role:
    name: ansible-aws-iam
    tasks_from: "user.yml"
  vars:
    iam_username: "{{ item.name }}"
    iam_user_tmp_pass: "{{ iam_tmp_pass }}"
    iam_user_groups: "{{ item.groups }}"
  with_items: "{{ iam_users }}"
```

Author Information
------------------

[Agustín García Flores](https://www.linkedin.com/in/agust%C3%ADn-garc%C3%ADa-flores-bb9aa975/)
