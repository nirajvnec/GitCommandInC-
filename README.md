var list = new List<string> { "apple", "banana", "cherry", "apple" };

// Create a dictionary with a case-insensitive string comparer to prevent duplicates
var dictionary = list.ToDictionary(x => x, StringComparer.OrdinalIgnoreCase);

// Or, create a dictionary with a custom equality comparer
var dictionary2 = list.ToDictionary(x => x, new CustomEqualityComparer());

// Custom equality comparer class
public class CustomEqualityComparer : IEqualityComparer<string>
{
    public bool Equals(string x, string y)
    {
        return x.ToLower() == y.ToLower();
    }

    public int GetHashCode(string obj)
    {
        return obj.ToLower().GetHashCode();
    }
}





public static T GetValueOrThrowException<T>(this T value, string paramName = null)
{
    if (string.IsNullOrEmpty(Convert.ToString(value)))
    {
        if (string.IsNullOrEmpty(paramName))
        {
            paramName = typeof(T).Name;
        }

        throw new ArgumentException($"{paramName} is null or empty.");
    }

    return value;
}


public static bool FieldMatches<T>(this DataRow row, string fieldName, T value)
{
    // Check if the value is null or empty
    if (value == null || (value is string && string.IsNullOrEmpty((string)(object)value)))
    {
        return true; // Match all values when the filter value is null or empty
    }

    // Compare the field value to the filter value
    object fieldValue = row[fieldName];
    if (fieldValue == null || fieldValue == DBNull.Value)
    {
        return false; // Not a match if the field value is null or DBNull.Value
    }
    else
    {
        T typedValue = (T)Convert.ChangeType(fieldValue, typeof(T));
        return typedValue != null && typedValue.Equals(value);
    }
}


using System;
using System.Collections.Generic;
using System.IO;

class Program
{
    static Dictionary<string, string> CsvToDictionary(string filename)
    {
        Dictionary<string, string> dictionary = new Dictionary<string, string>();

        using (StreamReader reader = new StreamReader(filename))
        {
            while (!reader.EndOfStream)
            {
                string line = reader.ReadLine();
                string[] values = line.Split(',');

                string key = values[0];
                string value = values[1];

                dictionary.Add(key, value);
            }
        }

        return dictionary;
    }
}



using System;
using System.IO;
using System.Linq;
using NuGet.VisualStudio;

// Get the current solution directory
string solutionDirectory = Path.GetDirectoryName(_dte.Solution.FullName);

// Get the package restore manager for the solution
IPackageRestoreManager packageRestoreManager = ServiceLocator.GetInstance<IPackageRestoreManager>();
if (packageRestoreManager == null)
{
    throw new InvalidOperationException("Package restore manager not found.");
}

// Check if package restore is required
if (packageRestoreManager.IsCurrentSolutionRestoreRequired())
{
    // Restore packages for the solution
    packageRestoreManager.RestoreMissingPackages(solutionDirectory, packageRestoreManager.GetPackagesConfigPaths(solutionDirectory).First());

    // Refresh the solution explorer window to show the restored packages
    _dte.Windows.Item(Constants.vsWindowKindSolutionExplorer).Activate();
    _dte.ActiveWindow.Object.GetItem("").Select(vsUISelectionType.vsUISelectionTypeSelect);
    _dte.Commands.Raise("{1496A755-94DE-11D0-8C3F-00C04FC2AAE2}", 222, null, null);
}




using Microsoft.Build.Evaluation;

// Load the project file
Project project = new Project(@"M:\udm1\frtbmodels\MARSfrtbmodels\FRTBSA.DRCMarsModel\MARSfrtbmodels\FRTBSA.DRCMarsModel.csproj");

// Set the "Start External Program" property
project.SetProperty("StartExternalProgram", @"M:\udm1\marsfrtb\UDMcontainer\ModelTestRunner\bin\ModelTestRunner.exe");

// Set the "Command Line Arguments" property
project.SetProperty("StartArguments", @"M:\udm1\frtbmodels\MARSfrtbmodels.TestCases\FX_DELTA_ENRICH M:\udm1\frtbmodels\MARSfrtbmodels.TestCases\FX_DELTA_ENRICH M:\udm1\frtbmodels\MARSfrtbmodels\FRTBSA.DRCMarsModel\bin --PREFERLOCALANDUPDATECSV: M:\udm1\frtbmodels\MARSfrtbmodels.TestCases\FX_DELTA_ENRICH --LOGMODE:trace");

// Save the changes
project.Save();






using System;
using System.IO;
using System.Threading.Tasks;

string directoryPath = @"M:\";
string searchPattern = "*.pfx";

Parallel.ForEach(Directory.GetFiles(directoryPath, searchPattern, SearchOption.AllDirectories), (filePath) =>
{
    Console.WriteLine(filePath);
});



using System.IO;

string drivePath = @"M:\";
string searchPattern = "*.pfx";

string[] files = Directory.GetFiles(drivePath, searchPattern, SearchOption.AllDirectories);

foreach (string file in files)
{
    Console.WriteLine(file);
}




using System.IO;

string searchPattern = "*.pfx";

DriveInfo[] allDrives = DriveInfo.GetDrives();
foreach (DriveInfo drive in allDrives)
{
    if (drive.IsReady)
    {
        string[] files = Directory.GetFiles(drive.RootDirectory.FullName, searchPattern, SearchOption.AllDirectories);
        foreach (string file in files)
        {
            Console.WriteLine(file);
        }
    }
}






using LibGit2Sharp;

// specify the URL of the repository you want to clone
var repoUrl = "https://github.com/exampleuser/example-repo.git";

// specify the local directory where you want to clone the repository
var localPath = @"C:\example\repo";

// specify the branch to checkout
var branchName = "develop";

// specify the credentials to use for authentication
var username = "myusername";
var password = "mypassword";

// clone the repository with additional options
var cloneOptions = new CloneOptions
{
    Checkout = branchName,
    CredentialsProvider = (_url, _user, _cred) => new UsernamePasswordCredentials
    {
        Username = username,
        Password = password
    }
};
Repository.Clone(repoUrl, localPath, cloneOptions);


















using LibGit2Sharp;

// specify the URL of the repository you want to clone
var repoUrl = "https://github.com/exampleuser/example-repo.git";

// specify the local directory where you want to clone the repository
var localPath = @"C:\example\repo";

// clone the repository
Repository.Clone(repoUrl, localPath);



# GitCommandInC-


using System;
using System.IO;
using LibGit2Sharp;

public static class GitFunctions
{
    public static void Gaa(Repository repo)
    {
        Commands.Stage(repo, "*");
    }

    public static void Gca(Repository repo)
    {
        Signature author = repo.Config.BuildSignature(DateTimeOffset.Now);
        Signature committer = author;
        Commit amendCommit = repo.Commit("Amend commit", author, committer);
        repo.Refs.UpdateTarget("HEAD", amendCommit.Id);
    }

    public static void Gcm(Repository repo)
    {
        Signature author = repo.Config.BuildSignature(DateTimeOffset.Now);
        Signature committer = author;
        repo.Commit("Commit changes", author, committer);
    }

    public static void Gcmm(Repository repo, string message)
    {
        Signature author = repo.Config.BuildSignature(DateTimeOffset.Now);
        Signature committer = author;
        repo.Commit(message, author, committer);
    }

    public static void Grm(Repository repo)
    {
        repo.Reset(ResetMode.Mixed, repo.Head.Tip.Parents.First());
    }

    public static void Gffm(Repository repo)
    {
        Commands.Fetch(repo, "origin", new string[] { "master:master" });
    }

    public static void Gpo(Repository repo, string args)
    {
        repo.Network.Push(repo.Head, args);
    }

    public static void Gfp(Repository repo, string args)
    {
        repo.Network.Push(repo.Head, args, new PushOptions { Force = true, PushModifiers = PushModifiers.LeaveHead });
    }

    public static void Gffp(Repository repo)
    {
        Commands.Pull(repo, repo.Config.BuildSignature(DateTimeOffset.Now), new PullOptions { FetchOptions = new FetchOptions() { Prune = true }, MergeOptions = new MergeOptions() { FastForwardStrategy = FastForwardStrategy.OnlyWhenFastForward } });
    }

    public static void Grbm(Repository repo)
    {
        Gffm(repo);
        Gss(repo);
        repo.Rebase.Start(repo.Branches["master"], repo.Head, null, null, null);
        repo.Stashes.Pop();
    }

    public static void Grbi(Repository repo, string args)
    {
        Gss(repo);
        repo.Rebase.Start(repo.Branches["master"], repo.Head, null, null, args);
        repo.Stashes.Pop();
    }

    public static void Grba(Repository repo)
    {
        repo.Rebase.Abort();
    }

    public static void Grbc(Repository repo)
    {
        repo.Rebase.Continue();
    }

    public static void Grh(Repository repo)
    {
        repo.Reset(ResetMode.Hard, repo.Head.Tip.Parents.First());
    }

    public static void Grs(Repository repo)
    {
        repo.Reset(ResetMode.Soft, repo.Head.Tip.Parents.First());
    }

    public static void Gss(Repository repo)
    {
        repo.Stashes.Add(new StashCreateOptions { Message = DateTime.Now.ToString() });
    }

    public static void Gwip(Repository repo, string args)
    {
        Gcmm(repo, $"WIP: {args}");
    }


    public static void Rb()
    {
        Console.WriteLine("STARTING BUILD OF ALL.sln.....");
        string canonicalPath = @"C:\stash\canonical";
        Repository repo = new Repository(canonicalPath);
        
        // Stop the "Canonical" service using WMI
        using (var managementObject = new System.Management.ManagementObject())
        {
            managementObject.Path = new System.Management.ManagementPath($"Win32_Service.Name='Canonical'");
            managementObject.InvokeMethod("StopService", null);
        }
        
        // Restore NuGet packages
        var nugetRestoreArgs = new[] { "restore" };
        var nugetRestoreProcess = new System.Diagnostics.Process
        {
            StartInfo = new System.Diagnostics.ProcessStartInfo
            {
                FileName = "nuget.exe",
                Arguments = string.Join(" ", nugetRestoreArgs),
                WorkingDirectory = canonicalPath,
                RedirectStandardOutput = true,
                UseShellExecute = false,
                CreateNoWindow = true
            }
        };
        nugetRestoreProcess.Start();
        Console.WriteLine(nugetRestoreProcess.StandardOutput.ReadToEnd());
        nugetRestoreProcess.WaitForExit();

        // Set environment variable
        Environment.SetEnvironmentVariable("DISABLE_MRASPECT", "true");

        // Build the solution using MSBuild
        var msbuildArgs = new[] {
            "All.sln", "/m", "/v:m", "/clp:PerformanceSummary;Summary",
            "/p:RunCodeAnalysis=false", "/p:RunAnalyzers=false",
            "/p:StyleCopEnabled=false", "/p:Configuration=Debug", "/p:Platform=x64"
        };
        var msbuildProcess = new System.Diagnostics.Process
        {
            StartInfo = new System.Diagnostics.ProcessStartInfo
            {
                FileName = @"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\MSBuild\Current\Bin\msbuild.exe",
                Arguments = string.Join(" ", msbuildArgs),
                WorkingDirectory = canonicalPath,
                RedirectStandardOutput = true,
                UseShellExecute = false,
                CreateNoWindow = true
            }
        };
        msbuildProcess.Start();
        Console.WriteLine(msbuildProcess.StandardOutput.ReadToEnd());
        msbuildProcess.WaitForExit();

        Console.WriteLine("Build of ALL.sln complete.");
    }





using System;
using LibGit2Sharp;
using Microsoft.Alm.Authentication;

class Program
{
    static void Main(string[] args)
    {
        string repositoryPath = "C:\\path\\to\\repository";
        string remoteUrl = "https://github.com/username/repository.git";
        
        // Initialize Git repository
        using (var repo = new Repository(repositoryPath))
        {
            // Get the GCM instance
            var credentialManager = new GnomeKeyring("git");

            // Get credentials for the remote URL
            var credentials = credentialManager.GetCredentials(new TargetUri(remoteUrl));

            // Set the credentials for the repository
            var options = new FetchOptions();
            options.CredentialsProvider = (_url, _user, _cred) => new UsernamePasswordCredentials
            {
                Username = credentials.Username,
                Password = credentials.Password
            };

            // Fetch the changes using the credentials
            repo.Network.Fetch(remoteUrl, options);
        }
    }
}


git credential-manager get


Open Visual Studio 2022 and create a new C# Console Application project.

In the Solution Explorer, right-click the project and select "Add" -> "New Item".

In the "Add New Item" dialog box, select "Code Snippet" from the "Visual C#" section and name the snippet "BasicClass".

Click "Add" to create the new snippet file.

Replace the default code in the new snippet file with the following code:

<CodeSnippet Format="1.0.0">
  <Header>
    <Title>Employee Class</Title>
    <Shortcut>employee</Shortcut>
    <Description>Creates a new Employee class with properties and methods.</Description>
    <Author>Your Name</Author>
    <SnippetTypes>
      <SnippetType>Expansion</SnippetType>
    </SnippetTypes>
  </Header>
  <Snippet>
    <Declarations>
      <Literal>
        <ID>name</ID>
        <ToolTip>The name of the employee.</ToolTip>
        <Default>John Doe</Default>
      </Literal>
      <Literal>
        <ID>id</ID>
        <ToolTip>The ID of the employee.</ToolTip>
        <Default>12345</Default>
      </Literal>
      <Literal>
        <ID>salary</ID>
        <ToolTip>The salary of the employee.</ToolTip>
        <Default>50000</Default>
      </Literal>
    </Declarations>
    <Code Language="csharp">
      <![CDATA[
public class Employee
{
    public string Name { get; set; } = "$name$";
    public int ID { get; set; } = $id$;
    public decimal Salary { get; set; } = $salary$;

    public void Work()
    {
        Console.WriteLine("Employee is working...");
    }

    public void TakeBreak()
    {
        Console.WriteLine("Employee is taking a break...");
    }
}
]]>
    </Code>
  </Snippet>
</CodeSnippet>


To use the code snippet in a Visual Studio class file, you can follow these steps:

Open a new or existing class file in Visual Studio.
Position the cursor where you want to insert the code snippet.
Type the shortcut for the code snippet (in this case, "empsnippet").
Press the "Tab" key twice to expand the snippet.
Replace the placeholders with your own values or code as needed.
Press "Enter" to finish inserting the code snippet.
After completing these steps, the code snippet will be inserted into your class file. You can modify it as needed for your specific use case.

