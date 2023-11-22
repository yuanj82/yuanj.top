---
title: Conda 的一些报错
date: 2023-11-08T16:56:08+08:00
slug: d74gf95jf8
tags: [Linux,Conda]
---

昨天重装了系统，WSL 也重装了一下，之前我使用 R 的时候是通过 conda 来管理环境的，R 包夜通过 conda 来安装，但是今天安装 R 包时出现一些问题。

<!--more-->

在安装 DEseq2 的时候，使用命令：

```bash
conda install -c bioconda bioconductor-deseq2
```

但是报错：

```bash
ERROR conda.core.link: _execute ( 730 ) : An error occurred while installing package 'bioconda:: bioconductor-genomeinfodbdata-1.2.10-r43hdfd78af_0'.
Rolling back transaction: done
class: LinkError
message:
post-link script failed for package bioconda:: bioconductor-genomeinfodbdata-1.2.10-r43hdfd78af_0
location of failed script: /home/unclecat4/conda/envs/r/bin/.bioconductor-genomeinfodbdata-post-link.sh
==> script messages <==
<None>
==> script output <==
stdout: /home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz": OK

stderr: ++ dirname -- /home/unclecat4/conda/envs/r/bin/installBiocDataPackage.sh
+ SCRIPT_DIR=/home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages
+ json=/home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
++ yq '."genomeinfodbdata-1.2.10".fn' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ FN='"GenomeInfoDbData_1.2.10.tar.gz"'
+ IFS=
+ read -r value
++ yq '."genomeinfodbdata-1.2.10".urls[]' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ URLS+= ( $value )
+ IFS=
+ read -r value
+ URLS+= ( $value )
+ IFS=
+ read -r value
+ URLS+= ( $value )
+ IFS=
+ read -r value
++ yq '."genomeinfodbdata-1.2.10".md5' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ MD5='"ebd78c10d672c0eb6c893d6135bdeca9"'
+ STAGING=/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10
+ mkdir -p /home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10
+ TARBALL='/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz"'
+ SUCCESS=0
+ for URL in ${URLS[@]}
++ tr -d '"'
++ echo '"https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz"'
+ URL=https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz
++ tr -d '"'
++ echo '"ebd78c10d672c0eb6c893d6135bdeca9"'
+ MD5=ebd78c10d672c0eb6c893d6135bdeca9
+ curl -L https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.4M  100 11.4M    0     0  3055k      0  0:00:03  0:00:03 --:--:-- 3056k
+ [[ 0 == 0 ]]
++ uname -s
+ [[ Linux == \L\i\n\u\x ]]
+ md5sum -c
+ SUCCESS=1
+ break
+ [[ 1 != 1 ]]
+ R CMD INSTALL --library=/home/unclecat4/conda/envs/r/lib/R/library '/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz"'
During startup - Warning messages:
1: package 『utils』 in options ( "defaultPackages" ) was not found
2: package 『stats』 in options ( "defaultPackages" ) was not found
Error: .onLoad failed in loadNamespace ( ) for 'utils', details:
  call: system ( paste ( which, shQuote ( names [i] )) , intern = TRUE, ignore.stderr = TRUE )
  error: error in running command

return code: 1

kwargs:
{}

Traceback ( most recent call last ) :
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1082, in __call__
    return func ( *args, **kwargs )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/main.py", line 87, in _main
    exit_code = do_call ( args, p )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/conda_argparse.py", line 84, in do_call
    return getattr ( module, func_name ) ( args, parser )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/main_install.py", line 20, in execute
    install ( args, parser, 'install' )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/install.py", line 316, in install
    handle_txn ( unlink_link_transaction, prefix, args, newenv )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/install.py", line 345, in handle_txn
    unlink_link_transaction.execute ( )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/core/link.py", line 281, in execute
    self._execute ( tuple ( concat ( interleave ( itervalues ( self.prefix_action_groups )) )) )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/core/link.py", line 744, in _execute
    raise CondaMultiError ( tuple ( concatv (
conda.CondaMultiErrorclass: LinkError
message:
post-link script failed for package bioconda:: bioconductor-genomeinfodbdata-1.2.10-r43hdfd78af_0
location of failed script: /home/unclecat4/conda/envs/r/bin/.bioconductor-genomeinfodbdata-post-link.sh
==> script messages <==
<None>
==> script output <==
stdout: /home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz": OK

stderr: ++ dirname -- /home/unclecat4/conda/envs/r/bin/installBiocDataPackage.sh
+ SCRIPT_DIR=/home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages
+ json=/home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
++ yq '."genomeinfodbdata-1.2.10".fn' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ FN='"GenomeInfoDbData_1.2.10.tar.gz"'
+ IFS=
+ read -r value
++ yq '."genomeinfodbdata-1.2.10".urls[]' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ URLS+= ( $value )
+ IFS=
+ read -r value
+ URLS+= ( $value )
+ IFS=
+ read -r value
+ URLS+= ( $value )
+ IFS=
+ read -r value
++ yq '."genomeinfodbdata-1.2.10".md5' /home/unclecat4/conda/envs/r/bin/../share/bioconductor-data-packages/dataURLs.json
+ MD5='"ebd78c10d672c0eb6c893d6135bdeca9"'
+ STAGING=/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10
+ mkdir -p /home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10
+ TARBALL='/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz"'
+ SUCCESS=0
+ for URL in ${URLS[@]}
++ tr -d '"'
++ echo '"https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz"'
+ URL=https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz
++ tr -d '"'
++ echo '"ebd78c10d672c0eb6c893d6135bdeca9"'
+ MD5=ebd78c10d672c0eb6c893d6135bdeca9
+ curl -L https://bioconductor.org/packages/3.17/data/annotation/src/contrib/GenomeInfoDbData_1.2.10.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.4M  100 11.4M    0     0  3055k      0  0:00:03  0:00:03 --:--:-- 3056k
+ [[ 0 == 0 ]]
++ uname -s
+ [[ Linux == \L\i\n\u\x ]]
+ md5sum -c
+ SUCCESS=1
+ break
+ [[ 1 != 1 ]]
+ R CMD INSTALL --library=/home/unclecat4/conda/envs/r/lib/R/library '/home/unclecat4/conda/envs/r/share/genomeinfodbdata-1.2.10/"GenomeInfoDbData_1.2.10.tar.gz"'
During startup - Warning messages:
1: package 『utils』 in options ( "defaultPackages" ) was not found
2: package 『stats』 in options ( "defaultPackages" ) was not found
Error: .onLoad failed in loadNamespace ( ) for 'utils', details:
  call: system ( paste ( which, shQuote ( names [i] )) , intern = TRUE, ignore.stderr = TRUE )
  error: error in running command

return code: 1

kwargs:
{}

: <exception str ( ) failed>

During handling of the above exception, another exception occurred:

Traceback ( most recent call last ) :
  File "/home/unclecat4/conda/bin/conda", line 13, in <module>
    sys.exit ( main ( ))
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/cli/main.py", line 155, in main
    return conda_exception_handler ( _main, *args, **kwargs )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1374, in conda_exception_handler
    return_value = exception_handler ( func, *args, **kwargs )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1085, in __call__
    return self.handle_exception ( exc_val, exc_tb )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1116, in handle_exception
    return self.handle_application_exception ( exc_val, exc_tb )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1132, in handle_application_exception
    self._print_conda_exception ( exc_val, exc_tb )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1136, in _print_conda_exception
    print_conda_exception ( exc_val, exc_tb )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/exceptions.py", line 1059, in print_conda_exception
    stderrlog.error ( "\n%r\n", exc_val )
  File "/home/unclecat4/conda/lib/python3.8/logging/__init__.py", line 1475, in error
    self._log ( ERROR, msg, args, **kwargs )
  File "/home/unclecat4/conda/lib/python3.8/logging/__init__.py", line 1589, in _log
    self.handle ( record )
  File "/home/unclecat4/conda/lib/python3.8/logging/__init__.py", line 1598, in handle
    if ( not self.disabled ) and self.filter ( record ) :
  File "/home/unclecat4/conda/lib/python3.8/logging/__init__.py", line 811, in filter
    result = f.filter ( record )
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/gateways/logging.py", line 61, in filter
    record.msg = record.msg % new_args
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/__init__.py", line 132, in __repr__
    errs.append ( e.__repr__ ( ))
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/__init__.py", line 71, in __repr__
    return '%s: %s' % ( self.__class__.__name__, text_type ( self ))
  File "/home/unclecat4/conda/lib/python3.8/site-packages/conda/__init__.py", line 90, in __str__
    return text_type ( self.message % self._kwargs )
ValueError: unsupported format character 'T' ( 0x54 ) at index 1921
```

起先怀疑是源的问题，后来从清华源换到阿里源还是不行，后来我想，可以使用 R 的 `install.packages` 函数来安装，但是使用该函数时报错：

```bash
Error in install.packages ( "BiocManager" ) :
  could not find function "install.packages
```

很纳闷，于是使用 Arch 的包管理器直接安装 R，依旧会报错，这时我注意到，启动 R 的时候，R 会报错：

```bash
During startup - Warning messages:
1: package 『utils』 in options ( "defaultPackages" ) was not found
2: package 『stats』 in options ( "defaultPackages" ) was not found
```

这时才发现，是 Arch 中缺少 `base-devel` 包，Debian 系中是`build-essential`，安装后即解决问题，DEseq2 也可以正常安装了。

conda 安装的效率一直不高，找到一个工具 mamba 可以加速安装，切换到 base 环境安装：

```bash
conda install mamba -n base -c conda-forge
```

后面在安装包的时候把 `conda install` 换成 `mamba install` 即可，其他命令照旧。
