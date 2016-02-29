> [AZURE.NOTE] Azure PowerShell 是兩個版本為 1.0 和 0.9.8 中目前可用的。 如果您有現有的指令碼，而且不想要立即變更它們，可以繼續使用 0.9.8 版本。 當使用 1.0 版本時，您應該在預先生產環境中仔細測試您的指令碼，然後才在生產環境中使用以避免產生非預期的影響。
>
> 1.0 cmdlet 遵循命名模式 {動詞}-AzureRm {名詞}。然而，名稱不包含 0.9.8 **Rm** (比方說，而不是 New-azureresourcegroup 的新增-AzureRmResourceGroup)。 使用 Azure PowerShell 0.9.8 時，您必須先啟用資源管理員模式執行 **Switch-azuremode AzureResourceManager** 命令。 1.0 不需要執行此命令。
>
> 新功能只會加入到 1.0 發行版本。 如需 1.0 版，包括如何安裝和解除安裝版本，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

