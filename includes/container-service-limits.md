| Erőforrás | Alapértelmezett korlát |
| --- | :--- |
| Csomópontok maximális száma fürtönként | 100 |
| Podok maximális száma csomópontonként ([alapszintű hálózatkezelés a Kubenet használatával][basic-networking]) | 110 |
| Podok maximális száma csomópontonként ([speciális hálózatkezelés az Azure CNI használatával][advanced-networking]) | 30<sup>1</sup> |
| Fürtök maximális száma előfizetésenként | 100 |

<sup>1</sup>Ez az érték az ARM-sablon üzembe helyezésével szabható testre. [Itt][arm-deployment-example] találhat néhány példát.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
