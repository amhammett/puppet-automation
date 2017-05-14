.PHONY: test

repo_name      = puppet-automation
vault_password = --vault-password-file ~/.secrets/$(repo_name).vault
python_path    = $(shell which python)
ansible_cmd    = AWS_PROFILE=$(profile) ansible-playbook $(vault_password)
ansible_local  = $(ansible_cmd) -i localhost, -e "ansible_python_interpreter=$(python_path)"

# defaults
profile ?= dev
region  ?= us-west-2

edit-secrets:
	ansible-vault $(vault_password) edit vars/secrets.yml

puppet-master-deploy:
	$(ansible_cmd) puppet-master-deploy.yml -e env=$(env) -e profile=$(profile) -e region=$(region) $(debug)

puppet-master-stack:
	$(ansible_local) puppet-master-stack.yml -e env=$(env) -e profile=$(profile) -e region=$(region) $(debug)
