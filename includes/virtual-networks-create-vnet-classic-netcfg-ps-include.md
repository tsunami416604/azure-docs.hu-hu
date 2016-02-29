## 如何使用 PowerShell 的網路設定檔建立 VNet

Azure 會使用 xml 檔案定義訂用帳戶所有可用的 VNet。 您可以下載這個檔案並加以編輯以進行修改，或刪除現有的 VNet，建立新的虛擬網路。 本文件將說明如何下載這個檔案 (下稱網路組態，或 Netcfg 檔案)，以及如何編輯該檔案，以建立新的 VNet。 檢查 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx) 若要深入了解網路組態檔。

若要利用 PowerShell 使用 Netcfg 檔案建立 VNet，請依照下列步驟執行。

1. 如果您從未使用 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 並遵循指示以登入 Azure，並選取您的訂閱結束。
2. 從 Azure PowerShell 主控台中，使用 **Get-azurevnetconfig** 指令程式可執行下列命令來下載網路組態檔。 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    預期的輸出：

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. 開啟您儲存的任何 XML 或文字編輯器應用程式，上述步驟 2 中的檔案，並尋找 **<VirtualNetworkSites>** 項目。 如果您有任何已建立的網路，每個網路將會變成自己 **<VirtualNetworkSite>** 項目。
4. 若要建立虛擬網路在此案例中所述，新增下列 XML 低於 **<VirtualNetworkSites>** 項目:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  儲存網路組態檔。
10. 從 Azure PowerShell 主控台中，使用 **組 AzureVnetConfig** cmdlet 來執行下列命令來上傳網路組態檔。 請注意輸出下命令，您應該會看到 **成功** 下 **OperationStatus**。 如果未看到上述輸出結果，請檢查 xml 檔是否有誤。

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    此為上述命令的預期輸出內容：

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. 從 Azure PowerShell 主控台中，使用 **Get-azurevnetsite** 指令程式來確認已執行下列命令來新增新的網路。 

        Get-AzureVNetSite -VNetName TestVNet

    此為上述命令的預期輸出內容：

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded
