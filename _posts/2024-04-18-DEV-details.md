---
tags: [dev]
---
## Yarn
when yarn install failed due to checksum mismatch
```bash
YARN_CHECKSUM_BEHAVIOR=update yarn
```
when yarn install failed due to unknown error
```bash
yarn install --inline-builds
```
yarn upgrade self to specific version
```bash
yarn set version ${version}
```
Node Sass not support for M1
```bash
terminal app > get Info > enable `Open using Rossetta` option
```
## VSCode
**husky yarn command not found**
```bash
# https://typicode.github.io/husky/#/?id=command-not-found
# ~/.huskyrc
# This loads nvm.sh and sets the correct PATH before running hook
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
**prettier-vscode-10.4.0 Error: Invalid version**
```bash
# 如果项目使用的是 yarn 且近期有人更新过 prettier 依赖则需要更新下 sdk
yarn dlx @yarnpkg/sdks
```
**Prettier A dynamic import callback was not specified**
```bash
restart vscode
```
**eslint 'yarn global dir' didn't return a value**
```json
# need to let eslint know the node path
# <root>/.vscode/settings.json
"eslint.nodePath": "./WEB/.yarn/sdks",
```
## oh-my-zsh
**debug mode**

`zsh -xv 2> >(tee ~/omz-debug.log &>/dev/null)`

**debug the session initialization**

`zsh -xvic exit &> ~/omz-debug.log`
## express-http-proxy
to debug with the proxy details
```bash
DEBUG=express-http-proxy yarn start:app
```
## TypeScript
when open tsconfig.json having a Typescript error "Cannot write file ... because it would overwrite input file."
```json
// try to add next line to tsconfig's compilerOptions
"outDir": "builder"
```
If find all references not working, try to add "disableSizeLimit": true to compilerOptions of tsconfig file
```json
"compilerOptions": {
  "noErrorTruncation": true,
  "disableSizeLimit": true
}
```
Type 'HTMLCollectionOf' is not an array type or a string type or does not have a 'Symbol.iterator' method that returns an iterator
```json
"compilerOptions": {
  "lib": [
    "dom",
    "dom.iterable", // to support iterator
    "es2017",
    "es2018.promise",
    "scripthost"
  ],
}
```

```javascript
declare module "*";
```
## Clear HSTS for Firefox

- about:support
- Profile Folder. Click Open Folder.
- close Firefox
- find and open the file SiteSecurityServiceState.txt
- delete records

## Git
to debug git commands, prefix with the following flags
```bash
GIT_CURL_VERBOSE=1 GIT_TRACE=1 [git commands]
```
to fix git pull with error: "cannot lock ref" or "unable to update local ref"
```bash
git gc --prune=now
```
## Webpack
### webpack-dev-server
when the `http2` option was enabled, there might be an `err_http2_protocol_error` error for multiple concurrency requests

when you enable `https` with your localhost server, but the client's webSocketURL value was configured with the localhost, there would be an error thrown: `WebSocket connection to 'ws://localhost:443/ws' failed`. To fix this, we need to change the devServer client's webSocketURL value to `auto://0.0.0.0:0/ws`.


## Copy file from ssh server
```bash
scp root@xmn02-i01-kbm01.lab.nordigy.ru:/tmp/clw-22.3.0.jar ./
```
## Copy file to ssh server
```bash
scp ./RingCentralMeetings.exe Administrator@web01-t02-aws02.int.rclabenv.com:/cygdrive/d
```
## Podman
**exec container process '/bin/sh': Exec format error (or another binary than bin/sh)**
This can happen when running a container from an image for another architecture than the one you are running on.
For example, if a remote repository only has, and thus send you, a linux/arm64 OS/ARCH but you run on linux/amd64.
[Luckily we can fix that, we can teach our VM how to run this format of executables by using qemu-user-static](https://edofic.com/posts/2021-09-12-podman-m1-amd64/)
```bash
podman machine ssh
sudo -i
rpm-ostree install qemu-user-static
systemctl reboot
```
## [Gitlab pipeline change env variables](https://docs.gitlab.com/ee/ci/variables/#pass-an-environment-variable-to-another-job)
```yaml
build:
  stage: build
  script:
    - echo "BUILD_VARIABLE=value_from_build_job" >> build.env
  artifacts:
    reports:
      dotenv: build.env

deploy:
  stage: deploy
  variables:
    BUILD_VARIABLE: value_from_deploy_job
  script:
    - echo "$BUILD_VARIABLE"  # Output is: 'value_from_build_job' due to precedence
```
## K8S
**How do you get a Kubernetes pod's name from its IP address?**
```bash
kubectl get --all-namespaces  --output json  pods | jq '.items[] | select(.status.podIP=="10.22.19.69")' | jq .metadata.name
```
How do you get a Kubernetes service's name from its clusterIp address?
```bash
kubectl get --all-namespaces services -o json | jq -r '.items[] | select(.spec.clusterIP=="10.74.193.43") | .metadata.name as $serviceName | .metadata.namespace as $namespace | $serviceName, $namespace'
```
how to print the image info of the specific pod?
```bash
kubectl get pod <POD_NAME> -n <NAMESPACE> -o json | jq '.spec.containers[] | .name, .image'

```
**Copy content from pod**
```bash
kubectl cp <some-namespace>/<some-pod>:/tmp/foo /tmp/bar

kubectl cp sct-xmn02/xmn02-i01-sct01-7c846c4c4d-2xdw2:/app/sdi-config-tool-server-22.1.0-SNAPSHOT.jar /tmp/sdi-config-tool-server-22.1.0-SNAPSHOT.jar
```

## **use tcpdump to capture**

1. `kubectl get pods -n gci-aqa-ams -o wide | grep gci01-c01`
![](https://cdn.nlark.com/yuque/0/2023/png/35422548/1680268953745-c9d01737-115f-4330-bee8-ab02c466a611.png#averageHue=%23151728&from=url&id=Az25Q&originHeight=178&originWidth=1674&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
2. `kubectl exec -it gci01-c01-ace11-698b967f7d-flckg -n gci-aqa-ams -- /bin/sh`
3. `cat /sys/class/net/eth0/iflink` (output is what we want: 553 for example)
4. `for i in /sys/class/net/veth*/ifindex; do grep -l 553 $i; done` (553由第3步得出) 这步需要到第一步得到的work node上执行
5. 得到虚拟网卡信息 (eg: veth154660e3)
6. `tcpdump -i veth154660e3 host 10.62.123.96 -w ./target.pcap` (在 work node 上面执行)

---

## etcd
[各种命令](../undefined)
SDI 组件用的 etcd 存储信息，证书位置在 /etc/kubernetes/pki/etcd
```bash
# 通过 docker 找出 etcd 的 container id
docker ps | grep etcd

# 进入容器
docker exec -it --user root 80fb6869aaee  /bin/sh

# 列出 work nodes
etcdctl --cacert=ca.crt --cert=server.crt --key=server.key member list

# 列出 endpoint status
etcdctl --cacert=ca.crt --cert=server.crt --key=server.key --endpoints=xmn02-i01-kbm01:2379,xmn02-i01-kbm02:2379,xmn02-i01-kbm03:2379 endpoint status

# check health for default endpoint
etcdctl --cacert=ca.crt --cert=server.crt --key=server.key endpoint health
```
## Get local IP
```bash
ipconfig getifaddr en0
```
## Find pid for the specific port (eg: 8080)
```bash
sudo lsof -i :8080
# or
netstat -vanp tcp | grep 8080
```
## Get system info
```bash
# basic system info
uname -a
# detailed of system hardware info
sudo lshw --short
```
## Node
Node.js has two module systems: CommonJS modules and ECMAScript modules.
Authors can tell Node.js to use the ECMAScript modules loader via the .mjs file extension, the package.json "type" field, or the --input-type flag. Outside of those cases, Node.js will use the CommonJS module loader.
### Differences between ES modules and CommonJS
**No require, exports, or module.exports**
In most cases, the ES module import can be used to load CommonJS modules.If needed, a require function can be constructed within an ES module using module.createRequire().
**No __filename or __dirname**
These CommonJS variables are not available in ES modules.
__filename and __dirname use cases can be replicated via import.meta.url.
**No Native Module Loading**
Native modules are not currently supported with ES module imports.
They can instead be loaded with module.createRequire() or process.dlopen.
**No require.resolve**
Relative resolution can be handled via new URL('./local', import.meta.url).
For a complete require.resolve replacement, there is a flagged experimental import.meta.resolve API.
Alternatively module.createRequire() can be used.
**No NODE_PATH**
NODE_PATH is not part of resolving import specifiers. Please use symlinks if this behavior is desired.
**No require.extensions**
require.extensions is not used by import. The expectation is that loader hooks can provide this workflow in the future.
**No require.cache**
require.cache is not used by import as the ES module loader has its own separate cache.
## XHR
The W3 sets forth the following guidelines in their XMLHttpRequest Level 2 document. Obviously varying levels of conformance across browsers are to be expected.
**Uploads:**
While the request entity body is being uploaded and the upload complete flag is false, queue a task to fire a progress event named progress at the XMLHttpRequestUpload object **about every 50ms or for every byte transmitted, whichever is least frequent.** - W3 XMLHttpRequest Level 2 (Bolded for emphasis)
**Downloads:**
When it is said to make progress notifications, while the download is progressing, queue a task to fire a progress event named progress **about every 50ms or for every byte received, whichever is least frequent.** - W3 XMLHttpRequest Level 2 (Bolded for emphasis)
## Debug
### cookie
```javascript
function debugAccess(obj, prop, debugGet){
  var origValue = obj[prop];
  Object.defineProperty(obj, prop, {
    get: function () {
      if ( debugGet )
        debugger;
      return origValue;
    },
    set: function(val) {
      debugger;
      return origValue = val;
    }
  });
};
debugAccess(document, 'cookie');
```
### JWL远程 debug
```bash
vim /opt/ringcentral/jwl/jboss-eap-6.3/wrapper/conf/wrapper.conf
# insert the following statement
wrapper.java.additional.50=-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=8787
# save and quit, then run this command
systemctl restart jwl

```
## DOM
### HTMLElement.offsetParent
offsetParent returns null in the following situations:

- The element or its parent element has the display property set to none.
- The element has the position property set to fixed (Firefox returns ).
- The element is or .
### DOMParser for unit test string contains html tags or entities
```javascript
const decodeEntities = (str: string) => new DOMParser().parseFromString(str, 'text/html').documentElement.textContent || '';
```

## Chrome
To view cookies of all sites
```
`chrome://settings/siteData
```
## GraphQL
**introspection**
```json
{
  __schema {
    description
    queryType {
      name
    }
    mutationType {
      name
    }
    subscriptionType {
      name
    }
    types {
      ...FullType
    }
    directives {
      name
      description
      locations
      args {
        ...InputValue
      }
    }
  }
}
fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args {
      ...InputValue
    }
    type {
      ...TypeRef
    }
    isDeprecated
    deprecationReason
  }
  inputFields {
    ...InputValue
  }
  interfaces {
    ...TypeRef
  }
  enumValues(includeDeprecated: true) {
    name
    description
    isDeprecated
    deprecationReason
  }
  possibleTypes {
    ...TypeRef
  }
}
fragment InputValue on __InputValue {
  name
  description
  type {
    ...TypeRef
  }
  defaultValue
}
fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

## Grep
`grep -rnw '/path/to/somewhere/' -e 'pattern'`

-r or -R is recursive

-n is line number

-w stands for match the whole word

-l (lower-case L) can be added to just give the file name of matching files

-e is the pattern used during the search

Along with these, --exclude, --include, --exclude-dir flags could be used for efficient searching:
```bash
# This will only search through those files which have `.c` or `.h` extensions:
grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"

# This will exclude searching all the files ending with .o extension:
grep --exclude=\*.o -rnw '/path/to/somewhere/' -e "pattern"

# For directories it's possible to exclude one or more directories using the `--exclude-dir` parameter. For example, this will exclude the dirs dir1/, dir2/ and all of them matching *.dst/:
grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/search/' -e "pattern"
```
