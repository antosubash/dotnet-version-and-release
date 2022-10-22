# dotnet-version-and-release

Sample to version and release dotnet application using conventional commits. If you want to know more about conventional commits, please check [this](https://www.conventionalcommits.org/en/v1.0.0/).

## Libraries used

- [Versionize](https://github.com/versionize/versionize) - To version the application
- [Husky.Net](https://github.com/alirezanet/husky.net) - To run git hooks in dotnet

## Adding Husky.Net

We will use Husky.Net to lint the commit message. this will make the commit message follow the conventional commits standard.

Follow the steps here to add Husky.Net to your project. <https://alirezanet.github.io/Husky.Net/guide/getting-started.html#installation>

### Add a pre-commit hook

We will add a pre-commit hook to lint the commit message. Husky.Net will run the pre-commit hook before the commit is made.

```bash
dotnet husky add pre-commit
```

This will add a pre-commit hook to your project. You can find the hook in the `.husky` folder. In the `pre-commit` file, you can add the commands you want to run before the commit is made. In our case, we will run a group of command. This is a good example in case you want to run multiple commands before the commit is made.

```bash
husky run -v --group "pre-commit"
```

### Add dotnet format hook

We will add a dotnet format hook to format the code before the commit is made. Husky.Net will run the dotnet format hook before the commit is made.

```bash
dotnet husky add format-cs
```

This will add a dotnet format hook to your project. You can find the hook in the `.husky` folder. In the `format-cs` file, you can add the commands you want to run before the commit is made. In our case, we will run dotnet format.

```bash
dotnet husky run --name "dotnet-format" --args "$1"
```

### Add commit lint hook

We will add a commit lint hook to lint the commit message before the commit is made. Husky.Net will run the commit lint hook before the commit is made.

```bash
dotnet husky add commit-lint
```

This will add a commit lint hook to your project. You can find the hook in the `.husky` folder. In the `commit-lint` file, you can add the commands you want to run before the commit is made. In our case, we will run commitlint.

```bash
husky run --name "commit-message-linter" --args "$1"
echo
echo Great work! 🥂
echo
```

### Add a commit message linter

We will add a commit message linter to lint the commit message. Husky.Net will run the commit message linter before the commit is made. Create a new folder in the `.husky` folder and name it `csx` (csx stands for C# script). In the `csx` folder, create a new file and name it `commit-lint.csx`. In the `commit-lint.csx` file, add the following code.

```csharp
using System.Text.RegularExpressions;

private var pattern = @"^(?=.{1,90}$)(?:build|feat|ci|chore|docs|fix|perf|refactor|revert|style|test|wip)(?:\(.+\))*(?::).{4,}(?:#\d+)*(?<![\.\s])$";
private var msg = File.ReadAllLines(Args[0])[0];

if (Regex.IsMatch(msg, pattern))
    return 0;

Console.ForegroundColor = ConsoleColor.Red;
Console.WriteLine("Invalid commit message");
Console.ResetColor();
Console.WriteLine("e.g: 'feat(scope): subject' or 'fix: subject'");
Console.ForegroundColor = ConsoleColor.Gray;
Console.WriteLine("more info: https://www.conventionalcommits.org/en/v1.0.0/");

return 1;
```

## Update the task runner

Now we have our hooks ready. We need to update the task runner to run the hooks. Open the `task-runner.json` file and update the tasks. 

```json
{
   "tasks": [
     {
       "name": "commit-message-linter",
       "command": "husky",
       "args": [
         "exec",
         ".husky/csx/commit-lint.csx",
         "--args",
         "${args}"
       ]
     },
     {
       "name": "dotnet-format",
       "group": "pre-commit",
       "command": "dotnet-format",
       "args": ["--include", "${staged}"],
       "include": ["**/*.cs"]
     }
   ]
}
```






### Add a commit-msg hook


## Adding Versionize

We will use Versionize to version the application. Versionize will use the commit message to determine the version of the application. It will also generate a changelog based on the commit messages and tag the commit with the version.

To get started add version to you csproj file.

```xml
    <Version>1.0.0</Version>
```

Now we can install the versionize tool.

```bash
dotnet tool install --global versionize
```
