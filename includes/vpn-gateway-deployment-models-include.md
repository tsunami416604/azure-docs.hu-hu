Fontos megérteni azt, hogy az Azure jelenleg két üzembe helyezési modellel működik: Resource Manager és klasszikus. Mielőtt elkezdené a konfigurációt, győződjön meg róla, hogy azokat az utasításokat és üzemi modellt használja, amelyben dolgozni szeretne. A két modell nem teljesen kompatibilis egymással.

Például, ha egy olyan virtuális hálózattal dolgozik, amelyet a klasszikus üzemi modell használatával hoztak létre, és kapcsolatot szeretne hozzáadni a VNethez, akkor olyan üzembe helyezési módokat kell használnia, amelyek a klasszikus üzemi modelkednek felelnek meg, nem pedig a Resource Managernek. Ha azonban olyan virtuális hálózattal dolgozik, amelyet a Resource Manager-alapú üzemi modell használatával hoztak létre, akkor olyan üzembe helyezési módokat kell használnia, amely a Resource Manager-alapú üzemi modellnek felel meg, nem a klasszikus modellnek.

További információ az üzembe helyezési modellekről: [Understanding Resource Manager deployment and classic deployment](../articles/resource-manager-deployment-model.md) (A Resource Manager-telepítés és a klasszikus telepítés ismertetése).



<!--HONumber=Nov16_HO2-->


