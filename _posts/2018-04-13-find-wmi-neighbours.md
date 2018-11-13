When PowerShell fails, this might help you to find your WMI peer

```
using System;
using System.IO;
using System.Management;

namespace SMAPIQuery
{
    class Program
    {
        static void Main(string[] args)
        { 
			for (int i = 1; i < 301; i++)
			{
				string remoteMachine = "ITD" + i.ToString();
				string strPathToTheExe = "notepad.exe";
				string Parameter = "";
				string usernameAndDomain = remoteMachine + "\\localadmin";
				string password = "abcd1234";

				try
				{
					ConnectionOptions connOptions = new ConnectionOptions();
					connOptions.Impersonation = ImpersonationLevel.Impersonate;
					connOptions.EnablePrivileges = true;

					connOptions.Username = usernameAndDomain; //It should be like domain\username
					connOptions.Password = password;

					ManagementScope manScope = new ManagementScope
					(String.Format(@"\\{0}\ROOT\CIMV2", remoteMachine), connOptions);
					manScope.Connect();
					ObjectGetOptions objectGetOptions = new ObjectGetOptions();
					ManagementPath managementPath = new ManagementPath("Win32_Process");
					ManagementClass processClass = new ManagementClass(manScope, managementPath, objectGetOptions);
					ManagementBaseObject inParams = processClass.GetMethodParameters("Create");
					inParams["CommandLine"] = strPathToTheExe;
					ManagementBaseObject outParams = processClass.InvokeMethod("Create", inParams, null);
					Console.WriteLine("Creation of the process returned: " + outParams["returnValue"]);
					Console.WriteLine("Process ID: " + outParams["processId"]);

				}
				catch (Exception ex)
				{
				Console.WriteLine("Exception occured in method runProcessOnRemoteMachine : " + ex.Message.ToString());
				}
			}
		}
     }
 }
```
