
# [Batch – dokumentáció](index.yml)

# Áttekintés
## [Mi az Azure Batch?](batch-technical-overview.md)

# Gyors útmutatók
## [Batch-feladat futtatása – parancssori felület](quick-create-cli.md)
## [Batch-feladat futtatása – portál](quick-create-portal.md)
## [Batch-feladat futtatása – .NET](quick-run-dotnet.md)
## [Batch-feladat futtatása – Python](quick-run-python.md)


# Oktatóanyagok

## [Párhuzamos fájlfeldolgozás – .NET](tutorial-parallel-dotnet.md)
## [Párhuzamos fájlfeldolgozás – Python](tutorial-parallel-python.md)
## [Jelenetek renderelése az Arnolddal](tutorial-rendering-cli.md)
## [Párhuzamos R-szimuláció](tutorial-r-doazureparallel.md)

# Példák
## Kódminták
### [Azure-kódminták](https://azure.microsoft.com/resources/samples/?service=batch)
### [Batch-mintatár](https://github.com/Azure/azure-batch-samples)
## [Azure CLI](cli-samples.md)

# Alapelvek
## [Fejlesztői funkciók](batch-api-basics.md)
## [API-k és eszközök](batch-apis-tools.md)
## [Kvóták és korlátozások](batch-quota-limit.md)

# Útmutatók
## Batch-fiókok kezelése
### [Batch-fiókok kezelése a portálon](batch-account-create-portal.md)
### [Batch-fiókok kezelése a Batch Management .NET használatával](batch-management-dotnet.md)

## Hitelesítés az Azure AD-vel
### [Azure AD a Batch szolgáltatással](batch-aad-auth.md)
### [Azure AD a Batch Managementtel](batch-aad-auth-management.md)

## [Alkalmazáscsomagok létrehozása](batch-application-packages.md)

## Készletek létrehozása és kezelése
### [Számítási csomópontok automatikus méretezése](batch-automatic-scaling.md)
### [Nagy számítási igényű virtuális gépek](batch-pool-compute-intensive-sizes.md)
### [Számítási csomópontokhoz való hozzáférés konfigurálása](pool-endpoint-configuration.md)
### [Készlet létrehozása egyéni rendszerképpel](batch-custom-images.md)
### [Készlet létrehozása egy virtuális hálózaton belül](batch-virtual-network.md)
### [Linuxos számítási csomópontok](batch-linux-nodes.md)
### [Alacsony prioritású virtuális gépek](batch-low-pri-vms.md)

## Munkák és feladatok kezelése
### [Feladatok előkészítési és befejezési műveletei](batch-job-prep-release.md)
### [Párhuzamosan futó csomóponti feladatok](batch-parallel-node-tasks.md)
### [Feladatok számlálása állapot szerint](batch-get-task-counts.md)
### [Tevékenységfüggőségek](batch-task-dependencies.md)
### [Felhasználói fiókok feladatok futtatásához](batch-user-accounts.md)
### [Batch-feladatok futtatása kód nélkül](batch-cli-templates.md)


## [Feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md)
### [Kimenet megőrzése Batch API-val](batch-task-output-files.md)
### [Kimenet megőrzése File Conventions-könyvtárral](batch-task-output-file-conventions.md)

## Számítási feladatok futtatása
### [MPI-feladatok](batch-mpi.md)
### [Tárolókhoz kapcsolódó számítási feladatok](batch-docker-container-workloads.md)
### [Renderelés](batch-rendering-service.md)
### [Adatfeldolgozás a Batch és a Data Factory segítségével](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)

## Erőforrások megfigyelése
### [Diagnosztikai naplózás](batch-diagnostics.md)
### [Hatékony listázó lekérdezések](batch-efficient-list-queries.md)

## Fejlesztői eszközök használata
### [Batch .NET használata](batch-dotnet-get-started.md)
### [Batch Python használata ](batch-python-tutorial.md)
### [Batch Node.js használata](batch-nodejs-get-started.md)
### [PowerShell-parancsmagok használata](batch-powershell-cmdlets-get-started.md)
### [Az Azure parancssori felület használata](batch-cli-get-started.md)

# Referencia

## [Azure CLI](/cli/azure/batch)
## [Azure PowerShell](/powershell/module/azurerm.batch)
## [.NET](/dotnet/api/overview/azure/batch)
## [Java](/java/api/overview/azure/batch)
## [Node.js](/javascript/api/overview/azure/batch)
## [Python](/python/api/overview/azure/batch)
## REST
### [Batch szolgáltatás](/rest/api/batchservice)
### [Batch Management](/rest/api/batchmanagement/)
## [Batch-elemzés](batch-analytics.md)
### [Készlet létrehozása esemény](batch-pool-create-event.md)
### [Készlet törlésének indítása esemény](batch-pool-delete-start-event.md)
### [Készlet törlése kész esemény](batch-pool-delete-complete-event.md)
### [Készlet átméretezésének indítása esemény](batch-pool-resize-start-event.md)
### [Készlet átméretezése kész esemény](batch-pool-resize-complete-event.md)
### [Tevékenység indítása esemény](batch-task-start-event.md)
### [Tevékenység kész esemény](batch-task-complete-event.md)
### [Tevékenység meghiúsult esemény](batch-task-fail-event.md)
## [Batch számítási csomópont környezeti változói](batch-compute-node-environment-variables.md)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)
## [A Batch fóruma](https://social.msdn.microsoft.com/Forums/en-us/home?forum=azurebatch)
## [Batch Shipyard](https://github.com/Azure/batch-shipyard)
## [Blog](https://blogs.technet.microsoft.com/windowshpc/)
## [HPC-megoldások az Azure-ban](../virtual-machines/linux/high-performance-computing.md?toc=%2fazure%2fbatch%2ftoc.json)
## [Díjszabás](https://azure.microsoft.com/pricing/details/batch/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Resource Manager-sablon](/azure/templates/microsoft.batch/batchaccounts)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=batch&updatetype=&platform=)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-batch)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=batch)


