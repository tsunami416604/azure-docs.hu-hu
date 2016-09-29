Az Azure Backup szolgáltatás két típusú tárolóval rendelkezik – a Backup-tárolóval és a Recovery Services-tárolóval. A Backup-tároló vált elsőként elérhetővé. Utána következett a Recovery Services-tároló a kibővített, Resource Managerrel történő üzembe helyezések támogatása érdekében. A Microsoft a Resource Managerrel történő üzembe helyezés használatát javasolja, ha nem kifejezetten Klasszikus üzembe helyezésre van szükség.

| **Környezet** | **Portál** | **Tároló** |
|-----------|------|-----|
|Klasszikus|[Klasszikus](https://manage.windowsazure.com)|Biztonsági mentés|
|Resource Manager|[Azure](https://portal.azure.com)|Recovery Services|

> [AZURE.NOTE] A Backup-tárolók nem tudják megvédeni a Resource Manager által üzembe helyezett megoldásokat. A klasszikus módon üzembe helyezett kiszolgálók és virtuális gépek védelmére Recovery Services-tárolót használhat.  


<!--HONumber=Sep16_HO4-->


