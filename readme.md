# Kyverno

Kyverno learning.

See environment folders for k8s for the setup - done using kustomize and helm chart.

Usefull links and repos:
* [kyverno policies repo](https://github.com/kyverno/policies/tree/main) - super-comprehensive set of policy examples and also demonstrates test setups.

# Concepts

* Setting up policies
* validation of items (containers, kustomizations etc)
* mutation
* generate
* test
* Reporting
* CI/CD setup

## Setting up policies

basic sample 

kubectl apply -f ./policies/check-team.yaml
clusterpolicy.kyverno.io/require-labels created

kubectl create deployment nginx --image=nginx
error: failed to create deployment: admission webhook "validate.kyverno.svc-fail" denied the request: 

resource Deployment/default/nginx was blocked due to the following policies 

require-labels:
  autogen-check-team: 'validation error: label ''team'' is required. rule autogen-check-team
    failed at path /spec/template/metadata/labels/team/'

get the policy report overview
kubectl get policyreport
NAME                                  PASS   FAIL   WARN   ERROR   SKIP   AGE
cpol-disallow-capabilities            0      1      0      0       0      3s
cpol-disallow-host-namespaces         1      0      0      0       0      3s
cpol-disallow-host-path               1      0      0      0       0      3s
cpol-disallow-host-ports              1      0      0      0       0      3s
cpol-disallow-host-process            1      0      0      0       0      3s
cpol-disallow-privileged-containers   1      0      0      0       0      3s
cpol-disallow-proc-mount              1      0      0      0       0      3s
cpol-disallow-selinux                 2      0      0      0       0      3s
cpol-require-labels                   1      0      0      0       0      3s
cpol-restrict-apparmor-profiles       1      0      0      0       0      3s
cpol-restrict-seccomp                 1      0      0      0       0      3s
cpol-restrict-sysctls                 1      0      0      0       0      3s

Clean up

kubectl delete clusterpolicy require-labels

For audit the test is the same just use the -audit yaml.

Note that when you apply, you do not get any warnings or notices when you deploy the resource (nginx).

You have to go describe the policy report and find the error.  In live environments there would be monitoring.

kubectl describe policyreport cpol-require-labels-audit 



## Validation of items (containers, kustomizations etc)


## Mutation

This is a very powerful feature which will validate a rule and apply changes to the violating resources to make the rule right.

This is done via patching which has essentially the same format as kustomize.  In fact mutations can be applied to kustomizations as well!

When paired with validation rules, mutations make a powerful way to impose compliance on your clusters.  As always there are always caveats... the mutations can "get lost in the noise" and cause confusion when troubleshooting deployment issues for example so test them well and use them with care.

### patchStrategicMerge

This is the most common approach.

Items which do not exist are added.  Items which do exist are not modified.  See the mutate-team-label sample.



## Generate

This is super-sync by any other definition and is very comprehensive.

Keep the docs close!


commands

kubectl get updaterequests -A



## Test

[kyverno policies repo](https://github.com/kyverno/policies/tree/main) - super-comprehensive set of policy examples and also demonstrates test setups.

Done thru the CLI "test" feature and the layout and feel of the tests is very similar to common unit test frameworks.


## Reporting

the main element of these is policyreport.

kubectl get policyreport
NAME                                  PASS   FAIL   WARN   ERROR   SKIP   AGE
cpol-disallow-capabilities            0      1      0      0       0      3s
cpol-disallow-host-namespaces         1      0      0      0       0      3s
cpol-disallow-host-path               1      0      0      0       0      3s
cpol-disallow-host-ports              1      0      0      0       0      3s
cpol-disallow-host-process            1      0      0      0       0      3s
cpol-disallow-privileged-containers   1      0      0      0       0      3s
cpol-disallow-proc-mount              1      0      0      0       0      3s
cpol-disallow-selinux                 2      0      0      0       0      3s
cpol-require-labels                   1      0      0      0       0      3s
cpol-restrict-apparmor-profiles       1      0      0      0       0      3s
cpol-restrict-seccomp                 1      0      0      0       0      3s
cpol-restrict-sysctls                 1      0      0      0       0      3s

To get a list of reports in a namespace:

kubectl get policyreport

decribe to get the details.

## CI/CD setup

