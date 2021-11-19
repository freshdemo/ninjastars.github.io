---
layout: post
title:  "Ignore sensitive files in git!"
date:   2021-11-19 16:34:10 -0400
categories: infrastructure 
tags: infrastructure
---
<p>
When using tools like Terraform and repositories like Github together there is a danger in exposing too much data. Automation tools like Terraform typically require you to store account details and credentials. This is information you certainly do not want exposed publicly as it would completely expose your access.
</p>

<h3>Step 1 - Create a gitignore</h3>

<p>
I've placed a global gitignore file in my ~/Documents folder (you can put it anywhere) so that any repository worked with will not publish sensitive data. The file looks like this;
</p>

```
# Local .terraform directories
**/.terraform/*

# .tfstate files
*.tfstate
*.tfstate.*

# Crash log files
crash.log

# Ignore any .tfvars files that are generated automatically for each Terraform run. Most
# .tfvars files are managed as part of configuration and so should be included in
# version control.
#
# example.tfvars
terraform.tfvars

# Ignore override files as they are usually used to override resources locally and so
# are not checked in
override.tf
override.tf.json
*_override.tf
*_override.tf.json

# Include override files you do wish to add to version control using negated pattern
#
# !example_override.tf

# Include tfplan files to ignore the plan output of command: terraform plan -out=tfplan
# example: *tfplan*
*tfplan*
*.plan*
*lock*
```

<p>
You can see that this will prevent files like terraform.tfvars along with state files to be pushed to the repository.
<p>

<br>
<br>

<h3>Step 2 - Set the variable</h3>

<p>
Set the global variable for git to use this file, meaning that it will affect all repositories on this system.
<p>

```
git config --global core.excludesfile ~/Documents/.gitignore
```

<h3>Step 3 - Remove files accidentally pushed</h3>

<p>
If you have pushed a file to the repository that shouldn't be there you have a couple of options. You could destroy the entire repository and then re-initialize git on your localhost. Or you can remove the individual files with a command like this (replacing terraform.tfvars with the file you want removed.
</p>

```
git rm -f terraform.tfvars
```

<!-- Cloudflare Web Analytics --><script defer src='https://static.cloudflareinsights.com/beacon.min.js' data-cf-beacon='{"token": "3ff248322f9d497f8802ebf7d47130c1"}'></script><!-- End Cloudflare Web Analytics -->
