
az group create --name myRG --location eastus

az disk create \
  --resource-group myRG \
  --name mydisk \
  --size-gb 10 \
  --sku Premium_LRS \
  --location eastus


az vm create \
  --resource-group MyRG \
  --name myvm1 \
  --image Win2022Datacenter \
  --admin-username atul \
  --generate-ssh-keys

az vm create \
  --resource-group MyRG \
  --name myvm2 \
  --image Win2022Datacenter \
  --admin-username atul \
  --generate-ssh-keys

az vm disk attach \
  --resource-group myRG \
  --vm-name myvm1 \
  --name mydisk

az vm disk detach \
  --resource-group myRG \
  --vm-name myvm1 \
  --name mydisk

az vm disk attach \
  --resource-group myRG \
  --vm-name myvm2 \
  --name mydisk

az vm disk detach \
  --resource-group myRG \
  --vm-name myvm2 \
  --name mydisk

az disk delete \
  --resource-group myRG \
  --name mydisk \
  --yes

az vm delete \
  --resource-group MyRG \
  --name myvm1 \
  --yes

az vm delete \
  --resource-group MyRG \
  --name myvm2 \
  --yes

az group delete --name myRG --location --yes

