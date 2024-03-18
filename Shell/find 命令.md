# find 命令

## 案例

根据正则表达式查找文件，并将这些文件打包成 tar。

```sh
find -E . -iregex ".*\.(jar|aar|so)" -exec tar -cvf b.tar {} + ;
```



## 文档

### -exec

```
       -exec command ;
              Execute command; true if 0 status is returned.  All
              following arguments to find are taken to be arguments to
              the command until an argument consisting of `;' is
              encountered.  The string `{}' is replaced by the current
              file name being processed everywhere it occurs in the
              arguments to the command, not just in arguments where it
              is alone, as in some versions of find.  Both of these
              constructions might need to be escaped (with a `\') or
              quoted to protect them from expansion by the shell.  See
              the EXAMPLES section for examples of the use of the -exec
              option.  The specified command is run once for each
              matched file.  The command is executed in the starting
              directory.  There are unavoidable security problems
              surrounding use of the -exec action; you should use the
              -execdir option instead.

       -exec command {} +
              This variant of the -exec action runs the specified
              command on the selected files, but the command line is
              built by appending each selected file name at the end; the
              total number of invocations of the command will be much
              less than the number of matched files.  The command line
              is built in much the same way that xargs builds its
              command lines.  Only one instance of `{}' is allowed
              within the command, and it must appear at the end,
              immediately before the `+'; it needs to be escaped (with a
              `\') or quoted to protect it from interpretation by the
              shell.  The command is executed in the starting directory.
              If any invocation with the `+' form returns a non-zero
              value as exit status, then find returns a non-zero exit
              status.  If find encounters an error, this can sometimes
              cause an immediate exit, so some pending commands may not
              be run at all.  For this reason -exec my-
              command ... {} + -quit may not result in my-command
              actually being run.  This variant of -exec always returns
              true.
```

