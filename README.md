### Demo of issue fetching artifact containing ${osgi.platform} in name

## To reproduce issue:

- Ensure archive_override is commented out in MODULE.maven
- bazel run @maven//:pin

```
D:\bazel-rules-jvm-external-issue>bazel run @maven//:pin
DEBUG: C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl:404:10: The inputs to maven_install.json have changed, but the lock file has not been regenerated. Consider running 'bazel run @maven//:pin'
INFO: Repository rules_jvm_external~~maven~unpinned_maven instantiated at:
  <builtin>: in <toplevel>
Repository rule coursier_fetch defined at:
  C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl:1378:33: in <toplevel>
INFO: repository @@rules_jvm_external~~maven~unpinned_maven' used the following cache hits instead of downloading the corresponding file.
 * Hash '2b78bfdd3ef13fd1f42f158de0f029d7cbb1f4f652d51773445cf2b6f7918a87' for https://github.com/coursier/coursier/releases/download/v2.1.8/coursier.jar
If the definition of 'repository @@rules_jvm_external~~maven~unpinned_maven' was updated, verify that the hashes were also updated.
ERROR: An error occurred during the fetch of repository 'rules_jvm_external~~maven~unpinned_maven':
   Traceback (most recent call last):
        File "C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl", line 985, column 38, in _coursier_fetch_impl
                dep_tree = make_coursier_dep_tree(
        File "C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl", line 918, column 13, in make_coursier_dep_tree
                fail("Error while fetching artifact with coursier: " + exec_result.stderr)
Error in fail: Error while fetching artifact with coursier: OpenJDK 64-Bit Server VM warning: Options -Xverify:none and -noverify were deprecated in JDK 13 and will likely be removed in a future release.
Resolution error: Error downloading org.eclipse.platform:org.eclipse.swt.${osgi.platform}:[3.121.0]
  not found: https://repo1.maven.org/maven2/org/eclipse/platform/org.eclipse.swt.${osgi.platform}/maven-metadata.xml
ERROR: <builtin>: fetching coursier_fetch rule //:rules_jvm_external~~maven~unpinned_maven: Traceback (most recent call last):
        File "C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl", line 985, column 38, in _coursier_fetch_impl
                dep_tree = make_coursier_dep_tree(
        File "C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~/coursier.bzl", line 918, column 13, in make_coursier_dep_tree
                fail("Error while fetching artifact with coursier: " + exec_result.stderr)
Error in fail: Error while fetching artifact with coursier: OpenJDK 64-Bit Server VM warning: Options -Xverify:none and -noverify were deprecated in JDK 13 and will likely be removed in a future release.
Resolution error: Error downloading org.eclipse.platform:org.eclipse.swt.${osgi.platform}:[3.121.0]
  not found: https://repo1.maven.org/maven2/org/eclipse/platform/org.eclipse.swt.${osgi.platform}/maven-metadata.xml
ERROR: no such package '@@rules_jvm_external~~maven~unpinned_maven//': Error while fetching artifact with coursier: OpenJDK 64-Bit Server VM warning: Options -Xverify:none and -noverify were deprecated in JDK 13 and will likely be removed in a future release.
Resolution error: Error downloading org.eclipse.platform:org.eclipse.swt.${osgi.platform}:[3.121.0]
  not found: https://repo1.maven.org/maven2/org/eclipse/platform/org.eclipse.swt.${osgi.platform}/maven-metadata.xml
ERROR: C:/users/_bazel_username/cahkz2qs/external/rules_jvm_external~~maven~maven/BUILD:1920:6: @@rules_jvm_external~~maven~maven//:pin depends on @@rules_jvm_external~~maven~unpinned_maven//:pin in repository @@rules_jvm_external~~maven~unpinned_maven which failed to fetch. no such package '@@rules_jvm_external~~maven~unpinned_maven//': Error while fetching artifact with coursier: OpenJDK 64-Bit Server VM warning: Options -Xverify:none and -noverify were deprecated in JDK 13 and will likely be removed in a future release.
Resolution error: Error downloading org.eclipse.platform:org.eclipse.swt.${osgi.platform}:[3.121.0]
  not found: https://repo1.maven.org/maven2/org/eclipse/platform/org.eclipse.swt.${osgi.platform}/maven-metadata.xml
ERROR: Analysis of target '@@rules_jvm_external~~maven~maven//:pin' failed; build aborted: Analysis failed
INFO: Elapsed time: 9.415s, Critical Path: 0.01s
INFO: 1 process: 1 internal.
ERROR: Build did NOT complete successfully
ERROR: Build failed. Not running target
```

## To see potential fix:

- Ensure archive_override is enabled in MODULE.maven. This adds patches/rules_jvm_external.patch to rules_jvm_external 6.1
- del MODULE.bazel.lock
- note '--repo_env="COURSIER_OPTS=--pom-property osgi.platform=win32.win32.x86_64"' flag in .bazelrc
- bazel run @maven//:pin --config=with_coursier_opts
- bazel build //...

```
D:\bazel-rules-jvm-external-issue>bazel run @maven//:pin --config=with_coursier_opts
INFO: Analyzed target @@rules_jvm_external~~maven~maven//:pin (2 packages loaded, 4 targets configured).
INFO: Found 1 target...
Target @@rules_jvm_external~~maven~unpinned_maven//:pin up-to-date:
  bazel-bin/external/rules_jvm_external~~maven~unpinned_maven/pin
  bazel-bin/external/rules_jvm_external~~maven~unpinned_maven/pin.exe
INFO: Elapsed time: 12.957s, Critical Path: 0.01s
INFO: 1 process: 1 internal.
INFO: Build completed successfully, 1 total action
INFO: Running command line: bazel-bin/external/rules_jvm_external~~maven~unpinned_maven/pin.exe external/rules_jvm_external~~maven~unpinned_maven/unsorted_deps.json
Successfully pinned resolved artifacts for @rules_jvm_external~~maven~unpinned_maven, D:/bazel-rules-jvm-external-issue/maven_install.json is now up-to-date.

INFO: Analyzed target //:foo (81 packages loaded, 4062 targets configured).
INFO: Found 1 target...
Target //:foo up-to-date:
  bazel-bin/libfoo.jar
INFO: Elapsed time: 18.952s, Critical Path: 14.13s
INFO: 18 processes: 4 internal, 10 local, 4 worker.
INFO: Build completed successfully, 18 total actions
```
