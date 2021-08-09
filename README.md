# renovate-issue-11139-reproduction
Minimal reproduction of the Renovate issue 
[#11139](https://github.com/renovatebot/renovate/issues/11139): PIP requirements are case sensitive.

## How to reproduce
There's a minimal self-hosted set up like the one on which I reported the bug. Setting up a 
`RENOVATE_TOKEN` environment variable and running ` docker-compose run --rm renovate_test --dry-run`
will run it exactly as I was doing it.

## Behaviour
When running Renovate on this repository, it fails to lookup the `django` dependency.

<details><summary>Failing logs</summary>

```
DEBUG: Failed to look up dependency django (repository=josebama/renovate-issue-11139-reproduction, packageFile=requirements.txt, dependency=django)
DEBUG: Package releases lookups complete (repository=josebama/renovate-issue-11139-reproduction)
       "baseBranch": "main"
DEBUG: branchifyUpgrades (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: 0 flattened updates found:  (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: Returning 0 branch(es) (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: config.repoIsOnboarded=true (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: packageFiles with updates (repository=josebama/renovate-issue-11139-reproduction)
       "config": {
         "pip_requirements": [
           {
             "packageFile": "requirements.txt",
             "deps": [
               {
                 "depName": "django",
                 "currentValue": "==3.2.5",
                 "datasource": "pypi",
                 "currentVersion": "3.2.5",
                 "depIndex": 0,
                 "updates": [],
                 "warnings": [
                   {
                     "topic": "django",
                     "message": "Failed to look up dependency django"
                   }
                 ],
                 "versioning": "pep440"
               }
             ],
             "registryUrls": ["https://pypi.org/simple/"]
           }
         ]
       }
```

</details>

If I were to rename the dependency to `Django` instead, it would find it:

<details><summary>Logs with requirement set to Django instead of django</summary>

```
DEBUG: Package releases lookups complete (repository=josebama/renovate-issue-11139-reproduction)
       "baseBranch": "main"
DEBUG: branchifyUpgrades (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: 1 flattened updates found: Django (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: Returning 1 branch(es) (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: Fetching changelog: https://github.com/django/django (3.2.5 -> 3.2.6) (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: config.repoIsOnboarded=true (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: packageFiles with updates (repository=josebama/renovate-issue-11139-reproduction)
       "config": {
         "pip_requirements": [
           {
             "packageFile": "requirements.txt",
             "deps": [
               {
                 "depName": "Django",
                 "currentValue": "==3.2.5",
                 "datasource": "pypi",
                 "currentVersion": "3.2.5",
                 "depIndex": 0,
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "3.2.6",
                     "newValue": "==3.2.6",
                     "newMajor": 3,
                     "newMinor": 2,
                     "updateType": "patch",
                     "isRange": true,
                     "branchName": "renovate/django-3.x"
                   }
                 ],
                 "warnings": [],
                 "versioning": "pep440",
                 "sourceUrl": "https://github.com/django/django",
                 "changelogUrl": "https://github.com/django/django/tree/master/docs/releases",
                 "isSingleVersion": true,
                 "fixedVersion": "3.2.5"
               }
             ],
             "registryUrls": ["https://pypi.org/simple/"]
           }
         ]
       }
```

</details>

This seems to only happen when setting a specific `registryUrls` in the config, even if it is the 
official registry. If I remove it, it also works fine with the `django` dependency:



<details><summary>Logs</summary>

```
DEBUG: Package releases lookups complete (repository=josebama/renovate-issue-11139-reproduction)
       "baseBranch": "main"
DEBUG: branchifyUpgrades (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: 1 flattened updates found: django (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: Returning 1 branch(es) (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: Fetching changelog: https://github.com/django/django (3.2.5 -> 3.2.6) (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: config.repoIsOnboarded=true (repository=josebama/renovate-issue-11139-reproduction)
DEBUG: packageFiles with updates (repository=josebama/renovate-issue-11139-reproduction)
       "config": {
         "pip_requirements": [
           {
             "packageFile": "requirements.txt",
             "deps": [
               {
                 "depName": "django",
                 "currentValue": "==3.2.5",
                 "datasource": "pypi",
                 "currentVersion": "3.2.5",
                 "depIndex": 0,
                 "updates": [
                   {
                     "bucket": "non-major",
                     "newVersion": "3.2.6",
                     "newValue": "==3.2.6",
                     "releaseTimestamp": "2021-08-02T06:28:33.000Z",
                     "newMajor": 3,
                     "newMinor": 2,
                     "updateType": "patch",
                     "isRange": true,
                     "branchName": "renovate/django-3.x"
                   }
                 ],
                 "warnings": [],
                 "versioning": "pep440",
                 "sourceUrl": "https://github.com/django/django",
                 "homepage": "https://www.djangoproject.com/",
                 "changelogUrl": "https://github.com/django/django/tree/master/docs/releases",
                 "isSingleVersion": true,
                 "fixedVersion": "3.2.5"
               }
             ]
           }
         ]
       }
```

</details>
