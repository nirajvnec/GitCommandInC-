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
