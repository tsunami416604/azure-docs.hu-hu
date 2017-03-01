## <a name="delete-a-recovery-services-vault-powershell"></a>Recovery Services-tároló törlése (PowerShell)

1. A Recovery Services-tároló beszerzése

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. A tároló törlése

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> A lehető legnagyobb körültekintéssel használja a fenti parancsot, ugyanis ha véletlenül töröl egy tárolót, az összes adat elveszik. Ez a művelet végleges, és nem vonható vissza.  




<!--HONumber=Feb17_HO3-->


