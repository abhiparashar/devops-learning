# Advanced Roadmap: from good to top 1%

Start this **after** [ROADMAP.md](ROADMAP.md) is finished.
The first roadmap teaches you to **use** the tools. This one teaches you **how they work underneath**, how to **debug them under pressure**, and how to **run them at work**.

Same rules as before:
- One small lesson at a time. You type all the code. I explain and review.
- Tick a box `[x]` only when you can explain the lesson in your own words **and** you have done the "Break it" drill.
- Lessons are saved in [`lessons/`](lessons/) in the same simple style.
- Same study order: **Docker → Kubernetes → the rest**.

## What makes the top 1%

| Most engineers | Top 1% |
|---|---|
| Know the commands | Know what happens underneath each command |
| Google the error | Read the logs, trace the problem, find the cause |
| Learn on toy apps | Apply every topic to a real app at work |
| Say "it's faster now" | Show numbers: before and after |
| Fix the outage | Fix it, write a postmortem, make sure it can't happen again |

How to read each lesson:
- **Learn**: the ideas we cover
- **Words**: new jargon you will know
- **Do**: the hands-on task you type yourself
- **Break it**: break something on purpose, then fix it. This is where real skill comes from
- **You can now**: the skill you walk away with

Every phase ends with a **Work project**: the same topic, applied to a real app from your job, with before/after numbers written down.

---

## Phase A0: Linux deep (what every tool runs on)

Why: Docker, Kubernetes, and AWS are all Linux underneath. When they break, the fix is often a Linux fix.

### A0.1 Boot and systemd
- **Learn**: what happens when a server starts, how services are started, kept alive, and logged
- **Words**: kernel, init, systemd, unit file, target, journald, restart policy
- **Do**: write a systemd unit for your own script, make it restart on failure, read its logs with `journalctl -u`
- **Break it**: make the script crash on start, find the reason only from `journalctl`
- **You can now**: run any program as a reliable service
- [ ] done

### A0.2 Processes deep
- **Learn**: how programs start other programs, what signals really do, why PID 1 is special
- **Words**: fork, exec, parent/child, zombie, orphan, SIGTERM vs SIGKILL, `/proc`
- **Do**: look inside `/proc/<pid>/` (status, open files, environment) of a running program
- **Break it**: create a zombie process, then clean it up through its parent
- **You can now**: explain exactly what `kill` does and why a container takes 10 seconds to stop
- [ ] done

### A0.3 Filesystem and disks
- **Learn**: how files are stored, why "disk full" happens even after you delete files
- **Words**: inode, mount, block device, `df` vs `du`, file descriptor, deleted-but-open file
- **Do**: `df -h`, `du -sh`, `lsof`, `mount`, `findmnt`
- **Break it**: fill a disk with a log file, delete it while a program still holds it open, find and free the space
- **You can now**: fix "No space left on device" at 3 a.m.
- [ ] done

### A0.4 Performance debugging
- **Learn**: CPU vs memory vs disk vs network bottlenecks, how to find which one is slow
- **Words**: load average, iowait, page cache, swap, OOM killer, USE method (Utilization, Saturation, Errors)
- **Do**: `top`, `htop`, `vmstat 1`, `iostat -x 1`, `free -m`, `dmesg`
- **Break it**: use `stress-ng` to max out CPU, then memory, and name the bottleneck from the numbers alone
- **You can now**: answer "why is the server slow?" with evidence
- [ ] done

### A0.5 Tracing programs
- **Learn**: watching what a program asks the kernel to do
- **Words**: system call (syscall), `strace`, `lsof`, `perf`, eBPF (light)
- **Do**: `strace -f` a program and read its file and network calls
- **Break it**: a program fails with a vague error; find the missing file or wrong permission with `strace`
- **You can now**: debug programs that give useless error messages
- [ ] done

### A0.6 Networking deep
- **Learn**: how a TCP connection opens and closes, what each packet does
- **Words**: TCP handshake (SYN, SYN-ACK, ACK), connection states, TIME_WAIT, MTU, routing table, `tcpdump`
- **Do**: capture a `curl` request with `tcpdump` and read the handshake line by line
- **Break it**: point an app at a closed port vs a blocked port; tell "connection refused" from "timeout"
- **You can now**: prove whether a problem is the app or the network
- [ ] done

### A0.7 DNS and TLS deep
- **Learn**: how a name becomes an address, how HTTPS proves who a server is
- **Words**: resolver, TTL, `/etc/resolv.conf`, certificate chain, CA, SNI, `openssl s_client`
- **Do**: `dig +trace`, read a site's certificate chain with `openssl s_client`
- **Break it**: serve an expired or wrong-name certificate and read the exact error
- **You can now**: debug "it works for me but not for them" DNS and certificate problems
- [ ] done

### A0.8 Firewalls and NAT
- **Learn**: how Linux filters and rewrites packets (Docker and Kubernetes use this heavily)
- **Words**: iptables, nftables, chain, rule, NAT, conntrack
- **Do**: list rules with `iptables -L -n -v` and `iptables -t nat -L -n`
- **Break it**: block a port with a rule, watch the app fail, find and remove the rule
- **You can now**: read the firewall rules Docker and Kubernetes create
- [ ] done

### A0.9 Advanced Bash
- **Learn**: scripts that fail safely instead of half-working
- **Words**: `set -euo pipefail`, `trap`, function, exit code, `shellcheck`
- **Do**: a backup script with logging, cleanup on exit, and a clear error message
- **Break it**: remove `set -e`, make one command fail, watch the script carry on and do damage
- **You can now**: write scripts you trust in production
- [ ] done

**Work project:** pick one real server at work. Write a one-page "health check" runbook (CPU, memory, disk, network, DNS, certs) and a script that checks all of it.

- [ ] **Phase A0 done**

---

## Phase A1: Programming for DevOps (Python, then Go)

Why: Bash is fine for 20 lines. Real automation (APIs, AWS, Kubernetes tools) needs a real language. Docker, Kubernetes, and Terraform are written in Go.

### A1.1 Python for automation
- **Learn**: files, JSON, YAML, running commands, command-line arguments
- **Words**: module, `subprocess`, `argparse`, virtual environment (venv)
- **Do**: a script that reads a YAML config and runs checks listed in it
- **Break it**: feed it a broken YAML file; make it print a clear error instead of a crash
- **You can now**: replace long Bash scripts with readable Python
- [ ] done

### A1.2 Talking to APIs
- **Learn**: how tools talk over HTTP, handling errors and limits
- **Words**: REST, status code, token, rate limit, retry with backoff, pagination
- **Do**: a script that lists all repos in a GitHub org (more than one page)
- **Break it**: use a wrong token, then hit the rate limit; handle both
- **You can now**: automate any tool that has an API
- [ ] done

### A1.3 AWS with boto3
- **Learn**: control AWS from Python
- **Words**: boto3, client, paginator, waiter
- **Do**: a report of untagged EC2 instances and old EBS snapshots
- **Break it**: run it with a role missing one permission; read the `AccessDenied` message and fix only that
- **You can now**: build cost and cleanup tools for your team
- [ ] done

### A1.4 Testing your tools
- **Learn**: making sure your scripts still work after changes
- **Words**: unit test, `pytest`, mock, linter, formatter
- **Do**: tests for the A1.3 report, with AWS faked
- **Break it**: change one function, watch a test catch it
- **You can now**: ship tools other people depend on
- [ ] done

### A1.5 Go basics
- **Learn**: why cloud tools are written in Go, one binary with no dependencies
- **Words**: package, module, `go build`, struct, error value
- **Do**: a small CLI that checks if a list of URLs is up
- **Break it**: build it for Linux on your Mac (`GOOS=linux GOARCH=amd64`) and run it in a container
- **You can now**: read the source code of Docker and Kubernetes tools
- [ ] done

### A1.6 Go concurrency
- **Learn**: doing many things at once safely
- **Words**: goroutine, channel, `WaitGroup`, timeout, context
- **Do**: check 100 URLs in parallel with a timeout each
- **Break it**: remove the timeout, point one URL at a server that never answers, watch the tool hang
- **You can now**: write fast tools that never get stuck
- [ ] done

**Work project:** build one CLI tool your team actually uses (cleanup report, health checker, or release helper), with tests and a README.

- [ ] **Phase A1 done**

---

## Phase A2: Git deep

### A2.1 Git internals
- **Learn**: how Git stores everything as objects
- **Words**: blob, tree, commit object, ref, SHA, `.git/` folder
- **Do**: `git cat-file -p` on a commit, its tree, and a file
- **Break it**: delete a branch with unpushed work, rescue it with `git reflog`
- **You can now**: never lose work in Git again
- [ ] done

### A2.2 Rewriting history safely
- **Learn**: clean up commits before sharing them
- **Words**: rebase, interactive rebase, squash, cherry-pick, force-with-lease
- **Do**: squash 5 messy commits into 2 clean ones
- **Break it**: rebase a shared branch, see the damage on a teammate's copy, recover it
- **You can now**: keep a clean history without hurting your team
- [ ] done

### A2.3 Branching strategies
- **Learn**: how teams ship from Git
- **Words**: trunk-based development, GitFlow, release branch, feature flag
- **Do**: compare both strategies for your team's real release process
- **Break it**: simulate a long-lived branch merging after 2 weeks; count the conflicts
- **You can now**: pick a branching strategy and defend it
- [ ] done

### A2.4 Repo rules and protection
- **Learn**: stop bad code reaching `main`
- **Words**: branch protection, required checks, CODEOWNERS, signed commits, monorepo vs polyrepo
- **Do**: protect `main` so it needs a passing CI run and one review
- **Break it**: try to push directly to `main` and watch it get rejected
- **You can now**: set up a repo the way serious teams do
- [ ] done

### A2.5 Hooks and secret scanning
- **Learn**: catch mistakes before they leave your laptop
- **Words**: Git hook, `pre-commit`, gitleaks
- **Do**: a pre-commit setup that runs a linter and a secret scanner
- **Break it**: try to commit a fake AWS key; watch it get blocked
- **You can now**: stop secret leaks at the source
- [ ] done

**Work project:** add branch protection, CODEOWNERS, and pre-commit secret scanning to one real repo at work.

- [ ] **Phase A2 done**

---

## Phase A3: Docker deep

Why: most people stop at writing a Dockerfile. The top 1% know a container is just a Linux process with walls around it.

### A3.1 Small images
- **Learn**: ship only what the app needs to run
- **Words**: multi-stage build, distroless, `alpine` vs `slim`, musl vs glibc, `.dockerignore`
- **Do**: take one image and measure its size at each step as you shrink it
- **Break it**: switch a Python app to `alpine` and watch a library fail to install (the musl trap)
- **You can now**: cut image size by 5–10× with reasons for every choice
- [ ] done

### A3.2 BuildKit and fast builds
- **Learn**: the modern Docker builder and its caching tricks
- **Words**: BuildKit, cache mount, secret mount, `docker buildx`, remote cache
- **Do**: use a cache mount for `pip`/`maven` downloads; time builds before and after
- **Break it**: pass a password with `ARG`, find it in `docker history`, then fix it with a secret mount
- **You can now**: make builds fast without leaking secrets
- [ ] done

### A3.3 Multi-arch images
- **Learn**: your Mac is ARM (arm64), most servers are Intel/AMD (amd64)
- **Words**: architecture, platform, manifest list, `--platform`, emulation (QEMU)
- **Do**: build one tag that works on both arm64 and amd64 with `buildx`
- **Break it**: build only for arm64, run it on amd64, read the `exec format error`
- **You can now**: build images that run anywhere
- [ ] done

### A3.4 Reproducible images
- **Learn**: the same Dockerfile should give the same image next month
- **Words**: digest, pinning, OCI image format, manifest, labels
- **Do**: pin the base image by digest (`@sha256:...`), inspect it with `docker manifest inspect`
- **Break it**: build with `FROM python:latest` today and a month later; compare what changed
- **You can now**: know exactly what is inside every image you ship
- [ ] done

### A3.5 Namespaces: what a container can see
- **Learn**: a container is a normal Linux process with a limited view of the system
- **Words**: namespace (pid, net, mnt, uts, ipc, user), `unshare`, `nsenter`
- **Do**: find a container's process from the host with `ps`, then enter its namespaces with `nsenter`
- **Break it**: run a container with `--pid=host` and see what it can now see
- **You can now**: explain what a container really is, in one sentence
- [ ] done

### A3.6 cgroups: how much a container can use
- **Learn**: how CPU and memory limits are enforced
- **Words**: cgroup, memory limit, CPU shares/quota, OOMKilled, exit code 137
- **Do**: run a container with `--memory=50m --cpus=0.5` and read its cgroup files
- **Break it**: make the app use 100 MB; watch it die with exit code 137 and find "OOMKilled" in `docker inspect`
- **You can now**: explain why a container died and set the right limits
- [ ] done

### A3.7 Overlayfs: how layers work on disk
- **Learn**: how many layers become one filesystem, and copy-on-write
- **Words**: overlayfs, lower dir, upper dir, merged dir, copy-on-write, storage driver
- **Do**: find a container's layer folders with `docker inspect` (GraphDriver) and look inside
- **Break it**: write a large file inside a container, watch host disk use grow, then remove it properly
- **You can now**: explain why containers lose data on delete and why big layers waste space
- [ ] done

### A3.8 The runtime stack
- **Learn**: what really happens when you type `docker run`
- **Words**: Docker CLI, dockerd, containerd, runc, shim, OCI runtime spec
- **Do**: run a container with `ctr` (containerd's own tool), skipping Docker
- **Break it**: build a "container" by hand with `unshare` + `chroot` + a cgroup, no Docker at all
- **You can now**: understand why Kubernetes dropped Docker and still runs your images
- [ ] done

### A3.9 Container networking deep
- **Learn**: how a container gets an IP and how `-p` really works
- **Words**: bridge, veth pair, `docker0`, NAT, user-defined network, embedded DNS (`127.0.0.11`)
- **Do**: trace a request from the host to a container: veth, bridge, iptables NAT rule
- **Break it**: put two containers on the default bridge and watch name lookup fail; fix it with a user-defined network
- **You can now**: debug "container can't reach X" problems
- [ ] done

### A3.10 PID 1 and graceful shutdown
- **Learn**: why some containers take 10 seconds to stop and lose requests
- **Words**: PID 1, signal forwarding, exec form vs shell form, `tini`, `--init`, `STOPSIGNAL`
- **Do**: an app that finishes in-flight work when it gets SIGTERM
- **Break it**: use shell-form `CMD`, run `docker stop`, time it (10 s, then SIGKILL); fix it
- **You can now**: ship containers that stop cleanly with zero lost requests
- [ ] done

### A3.11 Production runtime
- **Learn**: running containers for real, not just on a laptop
- **Words**: healthcheck, restart policy, logging driver, log rotation, `ulimit`
- **Do**: add a `HEALTHCHECK`, restart policy, and log rotation to your app
- **Break it**: let logs grow with no rotation until the disk fills; fix it
- **You can now**: run containers that heal themselves and never fill the disk
- [ ] done

### A3.12 Container security
- **Learn**: stop a hacked container from taking over the host
- **Words**: non-root user, rootless Docker, Linux capabilities, seccomp, read-only filesystem, `docker.sock` risk
- **Do**: run your app as non-root, with `--cap-drop=ALL`, `--read-only`, and no extra privileges
- **Break it**: mount `docker.sock` into a container and show it can control the whole host
- **You can now**: harden any container and explain each setting
- [ ] done

### A3.13 Supply chain
- **Learn**: prove what is in your image and who built it
- **Words**: CVE, Trivy, SBOM (software bill of materials), syft, image signing, cosign
- **Do**: scan with Trivy, make an SBOM with syft, sign the image with cosign
- **Break it**: use an old base image, count the critical CVEs, then fix them by upgrading
- **You can now**: ship images you can prove are safe
- [ ] done

**Work project:** take one real app from work through A3.1–A3.13. Write down before/after: image size, build time, number of critical CVEs, stop time.

- [ ] **Phase A3 done**

---

## Phase A4: CI/CD deep

### A4.1 Pipeline design
- **Learn**: pipelines that give fast, trustworthy answers
- **Words**: fast feedback, test pyramid, fail fast, parallel jobs, dependency cache, flaky test
- **Do**: split a slow pipeline into parallel jobs with caching; time it before and after
- **Break it**: add a flaky test, watch trust in CI drop, then quarantine and fix it
- **You can now**: make a 20-minute pipeline run in 5
- [ ] done

### A4.2 Reusable pipelines
- **Learn**: one pipeline template for many repos
- **Words**: reusable workflow, composite action, Jenkins shared library
- **Do**: move common steps into one reusable workflow used by two repos
- **Break it**: change the template in a breaking way; use version tags so repos don't break
- **You can now**: maintain CI for a whole team, not one repo
- [ ] done

### A4.3 Runners and agents
- **Learn**: the machines that run your pipelines, and their risks
- **Words**: self-hosted runner, ephemeral runner, Jenkins agent on Kubernetes, runner isolation
- **Do**: run ephemeral runners or Jenkins agents as Kubernetes pods
- **Break it**: show how a long-lived runner leaks files and secrets between jobs
- **You can now**: run CI safely at company scale
- [ ] done

### A4.4 No keys in CI (OIDC)
- **Learn**: CI logs into AWS without stored passwords
- **Words**: OIDC, identity provider, trust policy, short-lived credentials
- **Do**: GitHub Actions or Jenkins assumes an AWS role with OIDC; delete the old access keys
- **Break it**: let a different branch try to assume the role; lock the trust policy to `main`
- **You can now**: remove one of the most common cloud security holes
- [ ] done

### A4.5 Build once, promote everywhere
- **Learn**: the same image goes to dev, staging, and prod
- **Words**: artifact promotion, immutable tag, environment, approval gate
- **Do**: build once, deploy that exact digest to dev, then staging, then prod
- **Break it**: rebuild per environment, show the images differ, explain why that is dangerous
- **You can now**: guarantee prod runs exactly what was tested
- [ ] done

### A4.6 Progressive delivery
- **Learn**: release to a few users first, roll back automatically on errors
- **Words**: canary analysis, Argo Rollouts, automated rollback, feature flag
- **Do**: a canary release that checks error rate before going to 100%
- **Break it**: ship a version that returns errors; watch the rollout stop and roll back by itself
- **You can now**: release on a Friday without fear
- [ ] done

### A4.7 Measuring delivery
- **Learn**: numbers that show whether a team ships well
- **Words**: DORA metrics (deploy frequency, lead time, change failure rate, time to restore)
- **Do**: measure the four DORA numbers for your team
- **Break it**: find the single slowest step in your path to prod and fix it
- **You can now**: prove your CI/CD work made things better
- [ ] done

**Work project:** upgrade one real pipeline at work: caching, OIDC, build-once-promote. Write down build time and DORA numbers before and after.

- [ ] **Phase A4 done**

---

## Phase A5: AWS deep

### A5.1 IAM deep
- **Learn**: exactly how AWS decides "allow" or "deny"
- **Words**: policy evaluation, explicit deny, permission boundary, SCP, assume role, cross-account access
- **Do**: let a role in account A read one S3 bucket in account B, nothing more
- **Break it**: add an explicit deny and show it beats every allow; use the IAM policy simulator
- **You can now**: debug any `AccessDenied` error
- [ ] done

### A5.2 Multi-account setup
- **Learn**: why companies use many AWS accounts
- **Words**: AWS Organizations, OU, landing zone, Control Tower, IAM Identity Center (SSO)
- **Do**: design dev, staging, prod, and security accounts on paper, then build two of them
- **Break it**: block a region with an SCP, then try to launch something there
- **You can now**: set up AWS the way large companies do
- [ ] done

### A5.3 Networking deep
- **Learn**: VPC design for real apps, and its hidden costs
- **Words**: multi-AZ subnets, NAT gateway cost, VPC endpoint, peering, Transit Gateway, Route 53, VPC Flow Logs
- **Do**: add an S3 VPC endpoint so traffic skips the NAT gateway; compare cost
- **Break it**: remove a route, find the dropped traffic in Flow Logs
- **You can now**: design and debug AWS networks
- [ ] done

### A5.4 Compute choices
- **Learn**: picking the right place to run code
- **Words**: EC2, ECS, EKS, Lambda, Fargate, spot instance, Graviton (ARM)
- **Do**: run the same app on Fargate and on Graviton spot; compare cost
- **Break it**: have a spot instance taken away; make the app survive it
- **You can now**: choose compute with numbers, not habit
- [ ] done

### A5.5 Storage and databases
- **Learn**: the right storage for each job, and backups you can actually restore
- **Words**: S3 storage classes, lifecycle, EBS types, RDS vs Aurora vs DynamoDB, snapshot, point-in-time recovery
- **Do**: restore an RDS backup into a new database and time it
- **Break it**: "accidentally" delete a table; recover it with point-in-time recovery
- **You can now**: promise a restore time and keep the promise
- [ ] done

### A5.6 Resilience and disaster recovery
- **Learn**: surviving the loss of a server, a zone, or a region
- **Words**: RTO, RPO, backup/restore, pilot light, warm standby, active-active
- **Do**: write a DR plan for one app with RTO and RPO numbers
- **Break it**: simulate losing one AZ; check that the app stays up
- **You can now**: answer "what happens if us-east-1 goes down?"
- [ ] done

### A5.7 Event-driven AWS
- **Learn**: services that talk through messages instead of direct calls
- **Words**: SQS, SNS, EventBridge, Lambda, dead-letter queue, idempotency
- **Do**: S3 upload → EventBridge → Lambda → SQS pipeline
- **Break it**: make Lambda fail; see messages land in the dead-letter queue and replay them
- **You can now**: build systems that don't lose work when one part fails
- [ ] done

### A5.8 Well-Architected
- **Learn**: AWS's checklist for good systems
- **Words**: 6 pillars (operational excellence, security, reliability, performance, cost, sustainability)
- **Do**: review one of your systems against all 6 pillars
- **Break it**: pick the weakest pillar and show the failure it allows
- **You can now**: review any AWS design like a senior engineer
- [ ] done

**Work project:** run a Well-Architected review on one real system at work and fix the top 3 findings.

- [ ] **Phase A5 done**

---

## Phase A6: Infrastructure as Code deep

### A6.1 How Terraform thinks
- **Learn**: the dependency graph and the order Terraform builds things in
- **Words**: graph, implicit vs explicit dependency, `depends_on`, `lifecycle` (`create_before_destroy`, `prevent_destroy`)
- **Do**: draw the graph of your config with `terraform graph`
- **Break it**: change a field that forces "replace" on a database; protect it with `prevent_destroy`
- **You can now**: read a plan and spot danger before applying
- [ ] done

### A6.2 State surgery
- **Learn**: fixing state without breaking real resources
- **Words**: `import` block, `moved` block, `terraform state mv/rm`, drift, refresh
- **Do**: import a resource you made by hand, then rename it with a `moved` block
- **Break it**: change a resource in the console (drift); detect it with `terraform plan` and decide what to do
- **You can now**: bring hand-made infrastructure under code safely
- [ ] done

### A6.3 Module design
- **Learn**: modules other teams can use without reading the code
- **Words**: module versioning, input validation, composition, semantic versioning
- **Do**: a versioned VPC module with validated inputs, used by two environments
- **Break it**: release a breaking change; pin the version so nothing breaks
- **You can now**: build a module library for your company
- [ ] done

### A6.4 Many environments
- **Learn**: dev, staging, prod from the same code without copy-paste
- **Words**: workspace, directory per environment, Terragrunt, DRY
- **Do**: compare workspaces vs directories for your setup and pick one
- **Break it**: run `apply` in the wrong workspace; add guards so it can't happen again
- **You can now**: manage many environments safely
- [ ] done

### A6.5 Testing infrastructure code
- **Learn**: catch mistakes before they reach AWS
- **Words**: `terraform validate`, `tflint`, checkov, `terraform test`
- **Do**: add lint, security scan, and a `terraform test` to a module
- **Break it**: make a public S3 bucket; watch checkov block it
- **You can now**: ship infrastructure changes with confidence
- [ ] done

### A6.6 IaC in CI
- **Learn**: plan on every pull request, apply on merge
- **Words**: plan in PR, Atlantis, policy as code, OPA, approval
- **Do**: CI posts the `terraform plan` on each PR and applies after merge
- **Break it**: two people apply at once; show how state locking stops it
- **You can now**: run Terraform like a team, not one laptop
- [ ] done

### A6.7 Configured servers vs baked images
- **Learn**: setting up servers after they boot vs building ready-made images
- **Words**: Ansible role, idempotency, immutable infrastructure, Packer, golden image
- **Do**: one Ansible role, then the same setup baked into an AMI with Packer
- **Break it**: run the Ansible role twice; prove nothing changes the second time
- **You can now**: choose between configuring and baking, and explain why
- [ ] done

**Work project:** import one hand-clicked part of your work AWS into Terraform until `terraform plan` shows **no changes**.

- [ ] **Phase A6 done**

---

## Phase A7: Kubernetes deep

Why: anyone can `kubectl apply`. The top 1% know what each part of the cluster does, and fix it when it breaks.

### A7.1 Architecture deep
- **Learn**: what each part of the control plane does, and the "keep fixing until it matches" loop
- **Words**: API server, etcd, scheduler, controller manager, kubelet, reconcile loop, desired vs actual state
- **Do**: follow one `kubectl apply` all the way to a running container
- **Break it**: stop the scheduler on a kind cluster; watch new pods stay `Pending`
- **You can now**: explain what happens inside K8s for any command
- [ ] done

### A7.2 Build a cluster yourself
- **Learn**: how a cluster is put together
- **Words**: kubeadm, certificates, kubeconfig, static pod, join token
- **Do**: build a 3-node cluster with kubeadm on VMs
- **Break it**: let a certificate expire (or fake it); find and fix the error
- **You can now**: run and repair a cluster, not just use one
- [ ] done

### A7.3 Scheduling deep
- **Learn**: how K8s decides where a pod runs, and when it evicts pods
- **Words**: requests/limits, QoS classes, taints, tolerations, affinity, topology spread, PodDisruptionBudget, priority
- **Do**: spread 3 replicas across zones and keep at least 2 up during node drains
- **Break it**: ask for more CPU than any node has; read why the pod is `Pending`
- **You can now**: control where every pod lands
- [ ] done

### A7.4 Networking deep
- **Learn**: how pods reach each other and how Services really work
- **Words**: CNI, pod CIDR, kube-proxy, iptables/IPVS, CoreDNS, NetworkPolicy
- **Do**: trace a request from one pod to a Service to another pod
- **Break it**: add a deny-all NetworkPolicy; open only the traffic the app needs
- **You can now**: debug "pod can't reach service" problems
- [ ] done

### A7.5 Autoscaling
- **Learn**: adding pods and nodes when busy, removing them when quiet
- **Words**: HPA, VPA, Cluster Autoscaler, Karpenter, metrics-server
- **Do**: HPA on CPU, then load test and watch pods and nodes grow
- **Break it**: forget resource requests; show why the HPA can't work
- **You can now**: handle traffic spikes without paying for idle servers
- [ ] done

### A7.6 Security deep
- **Learn**: who can do what in the cluster, and what pods are allowed to do
- **Words**: RBAC, Role, ClusterRole, ServiceAccount, Pod Security Standards, admission controller, EKS Pod Identity / IRSA
- **Do**: give an app only the rights it needs, and AWS access without keys
- **Break it**: use the default ServiceAccount with wide rights; show what a hacked pod could do
- **You can now**: lock down a cluster
- [ ] done

### A7.7 GitOps
- **Learn**: Git is the single source of truth for what runs in the cluster
- **Words**: GitOps, Argo CD, sync, drift, app of apps, Kustomize
- **Do**: deploy your app with Argo CD from a Git repo
- **Break it**: change something with `kubectl edit`; watch Argo CD detect and undo it
- **You can now**: run deployments the way modern teams do
- [ ] done

### A7.8 Extending Kubernetes
- **Learn**: teach K8s new kinds of objects
- **Words**: CRD, custom resource, controller, operator, kubebuilder
- **Do**: write a tiny controller (Go, or Python with kopf) that reacts to your own custom resource
- **Break it**: crash your controller; show the resource is still saved and fixed when it comes back
- **You can now**: understand and build operators
- [ ] done

### A7.9 Troubleshooting drills
- **Learn**: a fixed method for every common failure
- **Words**: CrashLoopBackOff, ImagePullBackOff, Pending, OOMKilled, Evicted, `kubectl debug`, ephemeral container
- **Do**: a "debug checklist": events → describe → logs → exec/debug → node
- **Break it**: 10 broken setups (wrong image, bad probe, no memory, bad DNS, wrong selector, …); fix each in under 5 minutes
- **You can now**: fix broken pods fast, under pressure
- [ ] done

### A7.10 Day-2 operations
- **Learn**: keeping a cluster healthy for years
- **Words**: version upgrade, node drain, etcd backup/restore, multi-cluster
- **Do**: upgrade a cluster by one version with no app downtime
- **Break it**: delete a namespace; restore it from an etcd backup (or Velero)
- **You can now**: own a production cluster
- [ ] done

**Work project:** run one real work app on EKS through Argo CD with HPA, PodDisruptionBudget, and NetworkPolicy. Upgrade the cluster with zero downtime and write down the steps.

- [ ] **Phase A7 done**

---

## Phase A8: Observability and SRE

### A8.1 SLOs and error budgets
- **Learn**: promise a level of reliability, then spend the "allowed failure" wisely
- **Words**: SLI, SLO, SLA, error budget, 99.9% = about 43 minutes down per month
- **Do**: write SLOs for one app (availability and speed)
- **Break it**: burn the error budget on purpose in a test; decide what the team does next
- **You can now**: talk about reliability in numbers with managers
- [ ] done

### A8.2 Metrics deep
- **Learn**: the right kind of metric for each question
- **Words**: counter, gauge, histogram, label, cardinality, RED method, USE method, `rate()`, `histogram_quantile()`
- **Do**: add request count, errors, and latency histograms to your app
- **Break it**: add a label with user IDs; watch Prometheus memory explode (the cardinality trap)
- **You can now**: design metrics that answer real questions cheaply
- [ ] done

### A8.3 Distributed tracing
- **Learn**: follow one request through many services
- **Words**: OpenTelemetry, trace, span, context propagation, sampling
- **Do**: trace a request across two services and find the slow one
- **Break it**: drop the trace headers between services; watch the trace break in two
- **You can now**: find which service made a request slow
- [ ] done

### A8.4 Logging deep
- **Learn**: logs you can search, and their cost
- **Words**: structured logging (JSON), correlation ID, log level, retention, sampling
- **Do**: JSON logs with a request ID that also appears in traces
- **Break it**: turn on debug logging in prod and measure the cost and noise
- **You can now**: find one user's failed request among millions of lines
- [ ] done

### A8.5 Alerting that works
- **Learn**: alert on what users feel, not on every number
- **Words**: symptom vs cause alert, burn-rate alert, runbook, alert fatigue, paging
- **Do**: burn-rate alerts for your SLO, each with a runbook link
- **Break it**: count your current alerts that nobody acts on; delete or fix them
- **You can now**: build on-call that people don't hate
- [ ] done

### A8.6 Incident response
- **Learn**: what to do when production is on fire
- **Words**: incident commander, severity level, status page, timeline, blameless postmortem, action item
- **Do**: run a practice incident with roles and a timeline
- **Break it**: run one without roles; compare how messy it gets
- **You can now**: lead an incident calmly and write a good postmortem
- [ ] done

### A8.7 Chaos engineering
- **Learn**: break things on purpose, on your schedule, to find weak spots
- **Words**: chaos experiment, blast radius, steady state, game day, AWS FIS, LitmusChaos
- **Do**: a game day: kill pods, then a node, then add network delay
- **Break it**: that's the lesson; write down every surprise
- **You can now**: find failures before your users do
- [ ] done

### A8.8 Load testing and capacity
- **Learn**: how much traffic your system can take, and when to add more
- **Words**: load test, k6, throughput, p99 latency, saturation point, capacity plan
- **Do**: load test your app until it breaks; write down the breaking point
- **Break it**: find the first part that fails (DB, CPU, connections) and raise the limit
- **You can now**: say "we can handle 3× traffic" and prove it
- [ ] done

**Work project:** define SLOs for one real service at work, add burn-rate alerts with runbooks, run one game day, and write a postmortem for it.

- [ ] **Phase A8 done**

---

## Phase A9: Security deep (DevSecOps)

### A9.1 Threat modeling
- **Learn**: find how a system could be attacked before an attacker does
- **Words**: threat model, attack surface, STRIDE, trust boundary
- **Do**: threat-model your capstone app on one page
- **Break it**: pick the top threat and show the attack on a test setup
- **You can now**: lead security design reviews
- [ ] done

### A9.2 Supply chain security
- **Learn**: prove every image came from your pipeline and wasn't changed
- **Words**: SLSA, provenance, SBOM, signature, Kyverno, admission policy
- **Do**: cluster accepts only images signed by your CI
- **Break it**: try to deploy an unsigned image; watch it get rejected
- **You can now**: block tampered images from production
- [ ] done

### A9.3 Secrets deep
- **Learn**: short-lived secrets that rotate on their own
- **Words**: HashiCorp Vault, dynamic secret, lease, rotation, External Secrets Operator
- **Do**: app gets a database password from Vault that expires after 1 hour
- **Break it**: leak a secret in a test, then rotate it everywhere in minutes
- **You can now**: make a leaked password nearly useless
- [ ] done

### A9.4 Detection and audit
- **Learn**: notice when someone does something they shouldn't
- **Words**: CloudTrail, GuardDuty, K8s audit log, Falco, SIEM
- **Do**: a Falco rule that fires when someone opens a shell in a pod
- **Break it**: open a shell in a pod; check the alert fires
- **You can now**: detect attacks, not just prevent them
- [ ] done

### A9.5 Network security and zero trust
- **Learn**: never trust traffic just because it's inside the network
- **Words**: zero trust, mTLS, service mesh, Istio, Linkerd
- **Do**: mTLS between two services with Linkerd
- **Break it**: call a service from a pod outside the mesh; watch it get refused
- **You can now**: secure service-to-service traffic
- [ ] done

### A9.6 Compliance as code
- **Learn**: prove to auditors that systems follow the rules, automatically
- **Words**: CIS benchmark, kube-bench, AWS Config, Security Hub
- **Do**: run kube-bench and AWS Config rules; fix the top findings
- **Break it**: create a non-compliant resource; watch it get flagged
- **You can now**: pass audits without weeks of manual work
- [ ] done

**Work project:** add image signing + admission policy and Vault (or Secrets Manager rotation) to one real app at work.

- [ ] **Phase A9 done**

---

## Phase A10: System design, cost, and career

### A10.1 System design basics
- **Learn**: the building blocks of big systems
- **Words**: load balancer, cache, queue, read replica, sharding, CAP theorem, consistency
- **Do**: explain where each block fits in your capstone app
- **Break it**: remove the cache; measure what happens to the database
- **You can now**: discuss system design in senior interviews
- [ ] done

### A10.2 Design practice
- **Learn**: designing infrastructure out loud, step by step
- **Words**: requirements, back-of-envelope estimate, bottleneck, trade-off
- **Do**: design 3 systems: a URL shortener, a CI platform, a log pipeline
- **Break it**: for each design, name the first thing that fails at 10× traffic
- **You can now**: pass infrastructure design interviews
- [ ] done

### A10.3 Cost (FinOps)
- **Learn**: cloud cost is an engineering problem
- **Words**: FinOps, cost allocation tag, rightsizing, Savings Plans, spot, data transfer cost
- **Do**: find the top 5 costs in an AWS account and cut one by 30%+
- **Break it**: find the hidden NAT gateway / cross-AZ data transfer cost in a real bill
- **You can now**: save your company real money and show the number
- [ ] done

### A10.4 Platform engineering
- **Learn**: build a paved road so developers ship without asking DevOps
- **Words**: internal developer platform, golden path, self-service, Backstage
- **Do**: a template that creates a new service with repo, CI, and deploy in one step
- **Break it**: ask a teammate to use it with no help; fix every place they get stuck
- **You can now**: multiply your impact across a whole team
- [ ] done

### A10.5 Proving your skill
- **Learn**: being top 1% only counts if people can see it
- **Words**: portfolio, write-up, open source contribution, certification (CKA, CKS, AWS DevOps Professional)
- **Do**: publish one write-up per work project with before/after numbers
- **Break it**: explain one project to a non-engineer in 2 minutes; simplify until they get it
- **You can now**: show proof of your skill in any interview
- [ ] done

- [ ] **Phase A10 done**

---

## Phase A11: Advanced capstone

Take the Phase 9 project from [ROADMAP.md](ROADMAP.md) to production grade:

1. Multi-account AWS (dev, prod) built entirely with Terraform, planned in PRs
2. CI with OIDC (no keys), cached builds, multi-arch images, SBOM, signed images
3. EKS with Argo CD, HPA, Karpenter, PodDisruptionBudgets, NetworkPolicies
4. Cluster accepts only signed images; secrets come from Vault or Secrets Manager
5. Canary releases with automated rollback on error rate
6. SLOs, burn-rate alerts, traces, and structured logs
7. One game day (kill a node, lose an AZ) with a written postmortem
8. A cluster upgrade with zero downtime
9. A cost report with at least one saving you made
10. A public write-up with diagrams and every before/after number

- [ ] **Advanced capstone done**
