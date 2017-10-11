## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Egy Azure parancssori felület használatával klasszikus virtuális hálózat létrehozása
Az Azure CLI segítségével a parancssorból felügyelheti az erőforrásokat bármilyen Windows, Linux vagy OSX rendszert futtató számítógépről. Az alábbi lépésekkel hozhat létre egy VNetet az Azure CLI segítségével.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../articles/cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Futtassa az **azure network vnet create** parancsot egy VNet és egy alhálózat létrehozásához az alábbiak szerint. A kimenet után látható lista ismerteti a használt paramétereket.
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Várt kimenet:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. A létrehozni kívánt VNet neve. A mi esetünkben *TestVNet*
   * **-e (vagy--címtartomány)**. Virtuális hálózat címterület. A mi esetünkben *192.168.0.0*
   * **-i (vagy - cidr)**. Hálózati maszk CIDR formátumban. A mi esetünkben *16*.
   * **-n (vagy--alhálózatneve**). Az első alhálózat neve. A mi esetünkben *FrontEnd*.
   * **-p (vagy--ip-alhálózat-start)**. Kezdő IP-cím alhálózati vagy alhálózati címtartományt. A mi esetünkben *192.168.1.0*.
   * **-r (vagy--alhálózat-cidr)**. Hálózati maszk alhálózat CIDR formátumban. A mi esetünkben *24*.
   * **-l (vagy --location)**. Az Azure-régió, ahol a VNet létrejön. A mi esetünkben *USA középső RÉGIÓJA*.
3. Futtassa az **azure network vnet subnet create** parancsot egy alhálózat létrehozásához az alábbiak szerint. A kimenet után látható lista ismerteti a használt paramétereket.
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    A fenti parancs várható kimenete:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (vagy--vnet-name**. A VNet neve, ahol létrejön az alhálózat. A mi esetünkben *TestVNet*.
   * **-n (vagy --name)**. Az új alhálózat neve. A mi esetünkben *háttér*.
   * **-a (vagy --address-prefix)**. Az alhálózat CIDR-blokkja. Négy esetünkben *192.168.2.0/24*.
4. Futtassa az **azure network vnet show** parancsot az új vnet tulajdonságainak megtekintéséhez a lent látható módon.
   
            azure network vnet show
   
    A fenti parancs várható kimenete:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

