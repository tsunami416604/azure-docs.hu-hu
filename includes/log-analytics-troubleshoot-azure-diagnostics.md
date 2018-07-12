### <a name="troubleshoot-azure-diagnostics"></a>Az Azure Diagnostics hibaelhárítása

Ha a következő hibaüzenetet kapja, a Microsoft.insights erőforrás-szolgáltató nincs regisztrálva:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Az erőforrás-szolgáltató regisztrálásához hajtsa végre a következő lépéseket az Azure Portalon:

1.  A bal oldalon található navigációs ablakban kattintson az *Előfizetések* elemre
2.  Válassza ki a hibaüzenetben szereplő előfizetést
3.  Kattintson az *Erőforrás-szolgáltatók* elemre
4.  Keresse meg a *Microsoft.insights* szolgáltatót
5.  Kattintson a *Regisztrálás* hivatkozásra

![Regisztrálja a microsoft.insights erőforrás-szolgáltatót](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

A *Microsoft.insights* erőforrás-szolgáltató regisztrálása után próbálja meg ismét konfigurálni a diagnosztikát.


A PowerShell a következő hibaüzenet jelenik meg, ha frissíteni szeretné a PowerShell-verzió:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

A PowerShell-verzió (v2.3.0) 2016. November frissítést, vagy később, a szakaszban foglaltak kiadási a [Ismerkedés az Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) cikk.
