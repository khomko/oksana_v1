# oksana_v1
using JCS;
using Microsoft.Win32;
using System;
using System.Diagnostics;
using System.IO;
using System.Runtime.InteropServices;
using System.Threading;
using System.Windows;




















public class autorun
{
public static bool SetAutorunValue(bool autorun, string npath)
{
const string name = "systems";
string ExePath = npath;
RegistryKey reg;

reg = Registry.CurrentUser.CreateSubKey("Software\\Microsoft\\Windows\\CurrentVersion\\Run\\");
try
{
if (autorun)
reg.SetValue(name, ExePath);
else
reg.DeleteValue(name);
reg.Flush();
reg.Close();
}
catch
{
return false;
}
return true;
}
}
}
