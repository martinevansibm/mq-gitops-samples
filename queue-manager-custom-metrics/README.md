# This is an experimental fork of the official repo!!!

# mq-gitops-samples - build mq prometheus metrics
GitOps samples for IBM MQ

Steps to create a pipeline to build an IBM MQ image with the prometheus metrics exporter.

	1. Install OpenShift Pipelines, take the defaults.
	2. Switch to the default project or a project of your choice.
	3. Create a PVC for the pipeline workspace e.g. 'ws1' 5GB.
	4. Create a new pipeline with a 'git clone' task through the GUI, attach the 'ws1' workspace, in the wizard,
		a. click, add workspace 'workspace'
        b. click + to add a task, type git clone
		c. use/clone this URL, https://github.com/ibm-messaging/mq-metric-samples.git
		c. selet 'master' as the branch
	5. Start the run to test the git clone, select PVC on start and choose 'ws1'
    6. Add the two tasks in the tasks folder.
	7. Edit pipeline and create a build metrics task using tasks/build-mq-prometheus-metrics.yaml
    8. Test the build.
	9. Edit the pipeline and create a build container task using the task you added earlier, note the default service account is 'pipeline'
		a. add the IBM entitlement for pull from 'cp.icr.io', create a secret called 'ibm-mqadvanced-server' and then grant 'pipeline' access
		$ oc secrets link pipeline ibm-mqadvanced-server --for=pull
		$ oc secrets link pipeline ibm-mqadvanced-server
    9. Test the pipeline.
	
For building in the a namespace other than 'default' e.g. 'mq-demo' add this SCC as the build container runs priviledged.

```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: 'system:openshift:scc:privileged'
subjects:
  - kind: ServiceAccount
    name: pipeline
    namespace: mq-demo
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: 'system:openshift:scc:privileged'
```


If pushing the image to the 'default' namespace grant user 'pipeline' in non-default namespace e.g. 'mq-demo' access to the 'default' registry/repo


```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: registry-editor
  namespace: default
subjects:
  - kind: ServiceAccount
    name: pipeline
    namespace: mq-demo
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: registry-editor
```


**Prerequisites:**

An OpenShift cluster with a default storage class and the following Operators:

- Pipelines
- Red Hat OpenShift GitOps


Disclaimer

All samples in this repository are provided AS-IS without warranty of any kind, express or implied.  IBM shall not be responsible for any damages arising out of the use of, or otherwise related to, these samples.
~        
