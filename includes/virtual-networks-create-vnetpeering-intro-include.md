A virtuális hálózatok közötti társviszony-létesítés egy olyan mechanizmus, amely összeköt két, azonos régióban található virtuális hálózatot az Azure gerinchálózatán keresztül. A társviszony létrehozását követően a két virtuális hálózat egyetlen virtuális hálózatként fog megjelenni a csatlakoztatáshoz kapcsolódó műveletek mindegyike esetén. Ha a virtuális hálózatok közötti társviszony-létesítés még újdonság az Ön számára, olvassa el a [virtuális hálózatok közötti társviszony-létesítés áttekintésével](../articles/virtual-network/virtual-network-peering-overview.md) foglalkozó cikket.

A virtuális hálózatok közötti társviszony-létesítés jelenleg nyilvános előzetes verzióban érhető el, a használatához regisztrálnia kell az alábbi parancsokkal:

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!--HONumber=Sep16_HO4-->


