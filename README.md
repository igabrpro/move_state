# move_state

This is a guide on HOW to use move state in Terraform. The goal is to move already created resource to different module and to different state, without destroying it

## Requirements
Install Terraform - [instructions](https://www.terraform.io/downloads)


## Guide
1. First download the repo https://github.com/igabrpro/move_state.git
2. Change teh directory ```cd move_state```
3. Execute ```terraform init``` in order to download the necessary providers
4. Execute ```terraform apply``` in order to provision the code in main.tf
5. With the code running create new directory named module ```mkdir module```
6. Create module.tf in the module folder
7. Add the following content
```
resource "random_pet" "name" {
  length    = "2"
  separator = "-"
}

output "out" {
  value = random_pet.name.id
}
```
6. Edit file main.tf located in the main directory
```
module "move_state" {
  source = "./module"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${module.move_state.out}"
  }
}
```
8. Execute ```terrafrom init``` again to download the necessary plugins 
9. Execute  ```terraform state mv random_pet.name module.move_state.random_pet.name``` this comand means taht you are moving the resource random_pet.name to module move_state and adding random_pet.name to the module you should get the following output
```
Move "random_pet.name" to "module.move_state.random_pet.name"
Successfully moved 1 object(s).
```
11. Now if you execute ```terraform apply``` you will see that there are no changest that are going to be made
```
module.move_state.random_pet.name: Refreshing state... [id=equally-barely-glad-polliwog]
null_resource.hello: Refreshing state... [id=6625233169286461655]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
```
12. Use terraform destroy to clear all resources 
