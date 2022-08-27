# Домашнее задание к занятию "5.5. Оркестрация кластером Docker контейнеров на примере Docker Swarm"

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Любые вопросы по решению задач задавайте в чате учебной группы.

---

## Задача 1

Дайте письменые ответы на следующие вопросы:

- В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global?
Из документации Swarm

For a replicated service, you specify the number of identical tasks you want to run. For example, you decide to deploy an HTTP service with three replicas, each serving the same content.
Для replicated сервиса вы определяете кол-во заданий, которые хотите запустить

A global service is a service that runs one task on every node. There is no pre-specified number of tasks. Each time you add a node to the swarm, the orchestrator creates a task and the scheduler assigns the task to the new node. Good candidates for global services are monitoring agents, an anti-virus scanners or other types of containers that you want to run on every node in the swarm.
Global сервис - это сервис, который запускает одно задание на каждой ноде.

- Какой алгоритм выбора лидера используется в Docker Swarm кластере?
На примере ниже. Одна нода - лидер, а две другие (или одна, если нод, например, две в кластере) имеют статус reachable, т.е. при недоступности текущего лидера, 
происходит выбор нового лидера из нод со статусом Reachable.
```
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
095wn8utbs9oqx9zsoo0ucwto *   node01.netology.yc   Ready     Active         Leader           20.10.17
mknbcqfn9sreylpaqo8ibt3zd     node02.netology.yc   Ready     Active         Reachable        20.10.17
04amqqcbdjvlqv1x4n79bscq9     node03.netology.yc   Ready     Active         Reachable        20.10.17
ve0o4p0fc5bfvj93sqrrcewl3     node04.netology.yc   Ready     Active                          20.10.17
qjl55c02w3a1qv99k8m84jlpf     node05.netology.yc   Ready     Active                          20.10.17
yk9o4qt6d41ksvxdhhwxrfhk3     node06.netology.yc   Ready     Active                          20.10.17
```

- Что такое Overlay Network?
Overlay-сеть создает подсеть, которую могут использовать контейнеры в разных хостах swarm-кластера. Контейнеры на разных физических хостах могут обмениваться данными по overlay-сети (если все они прикреплены к одной сети).
Overlay-сеть использует технологию vxlan, которая инкапсулирует layer 2 фреймы в layer 4 пакеты (UDP/IP). При помощи этого действия Docker создает виртуальные сети поверх существующих связей между хостами, которые могут оказаться внутри одной подсети. Любые точки, которые
являются частью этой виртуальной сети, выглядят друг для друга так, будто они связаны поверх свича и не заботятся об устройстве основной физической сети.



## Задача 2

Создать ваш первый Docker Swarm кластер в Яндекс.Облаке

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
docker node ls
```
Решение:

- Воспользуемся существующим образом, сделанным в предыдущем ТЗ:
```
vagrant@vagrant:~$ yc compute image list
+----------------------+---------------+--------+----------------------+--------+
|          ID          |     NAME      | FAMILY |     PRODUCT IDS      | STATUS |
+----------------------+---------------+--------+----------------------+--------+
| fd8h3gl9c5ovputc9ntl | centos-7-base | centos | f2euv1kekdgvc0jrpaet | READY  |
+----------------------+---------------+--------+----------------------+--------+
```
- ID облакаid id = b1g0k29qecug0jk3jt4i  и ID folder b1g5fh031uiok7frafp8
```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ yc init
Welcome! This command will take you through the configuration process.
Pick desired action:
 [1] Re-initialize this profile 'default' with new settings
 [2] Create a new profile
Please enter your numeric choice: 1
Please go to https://oauth.yandex.ru/authorize?response_type=token&client_id=1a6990aa636648e9b2ef855fa7bec2fb in order to obtain OAuth token.

You have one cloud available: 'cloud-kapelman' (id = b1g0k29qecug0jk3jt4i). It is going to be used by default.
Please choose folder to use:
 [1] default (id = b1g5fh031uiok7frafp8)
 [2] Create a new folder
Please enter your numeric choice: 1
Your current folder has been set to 'default' (id = b1g5fh031uiok7frafp8).
Do you want to configure a default Compute zone? [Y/n] Y
Which zone do you want to use as a profile default?
 [1] ru-central1-a
 [2] ru-central1-b
 [3] ru-central1-c
 [4] Don't set default zone
Please enter your numeric choice: 1
Your profile default Compute zone has been set to 'ru-central1-a'.
```

- скопируем скрипты на виртуальную машину с утилитой яндекс облака
```
PS C:\Users\kapli\homeworks\05-virt-05-docker-swarm> scp C:\Users\kapli\homeworks\05-virt-05-docker-swarm\src.zip vagrant@192.168.33.10:/home/vagrant/05-virt-05-docker-swarm
vagrant@192.168.33.10's password:
src.zip                                                                                                                                                                        100%   34KB   8.4MB/s   00:00
```

- добавим переменные в файл variables.tf 
```
  GNU nano 4.8                                                                                      variables.tf                                                                                       Modified  # Заменить на ID своего облака
# https://console.cloud.yandex.ru/cloud?section=overview
variable "yandex_cloud_id" {
  default = "b1g0k29qecug0jk3jt4i"
}

# Заменить на Folder своего облака
# https://console.cloud.yandex.ru/cloud?section=overview
variable "yandex_folder_id" {
  default = "b1g5fh031uiok7frafp8"
}

# Заменить на ID своего образа
# ID можно узнать с помощью команды yc compute image list
variable "centos-7-base" {
  default = "fd8h3gl9c5ovputc9ntl"
}
```

- создадим сервисного пользователя из прошлог задания
```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ yc iam service-account create --name my-robot2
id: aje2e5p4jmfabmosensm
folder_id: b1g5fh031uiok7frafp8
created_at: "2022-08-26T21:54:18.519091905Z"
name: my-robot2
```

- зададим полномочия для сервисного пользователя
```
vagrant@vagrant:~/terraform-scripts$ yc resource-manager folder add-access-binding b1g5fh031uiok7frafp8 --role editor  --subject serviceAccount:aje2e5p4jmfabmosensm
done (1s)
```

- создание авторизационных ключей
```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ yc iam key create --service-account-name my-robot2 -o key.json
id: ajeinom9sfvqrj0is1nq
service_account_id: aje2e5p4jmfabmosensm
created_at: "2022-08-26T22:15:55.127247080Z"
key_algorithm: RSA_2048
```

- запустим terraform init и plan
```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of yandex-cloud/yandex...
- Finding latest version of hashicorp/null...
- Finding latest version of hashicorp/local...
- Installing hashicorp/null v3.1.1...
- Installed hashicorp/null v3.1.1 (signed by HashiCorp)
- Installing hashicorp/local v2.2.3...
- Installed hashicorp/local v2.2.3 (signed by HashiCorp)
- Installing yandex-cloud/yandex v0.77.0...
- Installed yandex-cloud/yandex v0.77.0 (self-signed, key ID E40F590B50BB8E40)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ terraform validate
Success! The configuration is valid.

vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.inventory will be created
  + resource "local_file" "inventory" {
      + content              = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "../ansible/inventory"
      + id                   = (known after apply)
    }

  # null_resource.cluster will be created
  + resource "null_resource" "cluster" {
      + id = (known after apply)
    }

  # null_resource.monitoring will be created
  + resource "null_resource" "monitoring" {
      + id = (known after apply)
    }

  # null_resource.sync will be created
  + resource "null_resource" "sync" {
      + id = (known after apply)
    }

  # null_resource.wait will be created
  + resource "null_resource" "wait" {
      + id = (known after apply)
    }

  # yandex_compute_instance.node01 will be created
  + resource "yandex_compute_instance" "node01" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node01.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node01"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node01"
              + size        = 10
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.11"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.node02 will be created
  + resource "yandex_compute_instance" "node02" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node02.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node02"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node02"
              + size        = 10
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.12"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.node03 will be created
  + resource "yandex_compute_instance" "node03" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node03.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node03"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node03"
              + size        = 10
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.13"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.node04 will be created
  + resource "yandex_compute_instance" "node04" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node04.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node04"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node04"
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.14"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.node05 will be created
  + resource "yandex_compute_instance" "node05" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node05.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node05"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node05"
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.15"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_compute_instance.node06 will be created
  + resource "yandex_compute_instance" "node06" {
      + allow_stopping_for_update = true
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = "node06.netology.yc"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCtnMd57eG81JsenyqtQJAL54D1++KL15Fy+OmgbQdJoAKCnA5TognKsmw/Xy9xCYyOmF8Ydi41YUNZY61iUq4qR3htkwCSu7br/9a/tMAyOJPVib5IIjzQX5tO970LpHOWaClTOvhmm63qEVbSbmGUpDNhPxaAOR2JGmapB6+L+EDRvY9YgPizIB1RhAMS5ryaTaY5/DJQq5rgB7dd7GpZ7Gkn8H1ELHJaMYarD9K/K/Avu7R780a8FuUgjvDdH+qncyVMkUpS+U8b4dXCsAUWj0BpPimXxVGnNup0oN+Q+yb+qC2IV8+pdWCexgjyadKB35M5UC7Wee2mqnaXMcep vagrant@vagrant
            EOT
        }
      + name                      = "node06"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8h3gl9c5ovputc9ntl"
              + name        = "root-node06"
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-nvme"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = "192.168.101.16"
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 4
          + memory        = 8
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_vpc_network.default will be created
  + resource "yandex_vpc_network" "default" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "net"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_subnet.default will be created
  + resource "yandex_vpc_subnet" "default" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "subnet"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.101.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

Plan: 13 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + external_ip_address_node01 = (known after apply)
  + external_ip_address_node02 = (known after apply)
  + external_ip_address_node03 = (known after apply)
  + external_ip_address_node04 = (known after apply)
  + external_ip_address_node05 = (known after apply)
  + external_ip_address_node06 = (known after apply)
  + internal_ip_address_node01 = "192.168.101.11"
  + internal_ip_address_node02 = "192.168.101.12"
  + internal_ip_address_node03 = "192.168.101.13"
  + internal_ip_address_node04 = "192.168.101.14"
  + internal_ip_address_node05 = "192.168.101.15"
  + internal_ip_address_node06 = "192.168.101.16"

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

- запустим terraform apply

```

Outputs:

external_ip_address_node01 = "51.250.91.110"
external_ip_address_node02 = "51.250.73.238"
external_ip_address_node03 = "51.250.86.176"
external_ip_address_node04 = "51.250.93.131"
external_ip_address_node05 = "51.250.93.32"
external_ip_address_node06 = "51.250.94.124"
internal_ip_address_node01 = "192.168.101.11"
internal_ip_address_node02 = "192.168.101.12"
internal_ip_address_node03 = "192.168.101.13"
internal_ip_address_node04 = "192.168.101.14"
internal_ip_address_node05 = "192.168.101.15"
internal_ip_address_node06 = "192.168.101.16"
```

- проверим, что запустился docker swarm

```
vagrant@vagrant:~/05-virt-05-docker-swarm/src/terraform$ ssh centos@51.250.91.110
[centos@node01 ~]$ docker node ls
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/nodes": dial unix /var/run/docker.sock: connect: permission denied
[centos@node01 ~]$ sudo docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
095wn8utbs9oqx9zsoo0ucwto *   node01.netology.yc   Ready     Active         Leader           20.10.17
mknbcqfn9sreylpaqo8ibt3zd     node02.netology.yc   Ready     Active         Reachable        20.10.17
04amqqcbdjvlqv1x4n79bscq9     node03.netology.yc   Ready     Active         Reachable        20.10.17
ve0o4p0fc5bfvj93sqrrcewl3     node04.netology.yc   Ready     Active                          20.10.17
qjl55c02w3a1qv99k8m84jlpf     node05.netology.yc   Ready     Active                          20.10.17
yk9o4qt6d41ksvxdhhwxrfhk3     node06.netology.yc   Ready     Active                          20.10.17
```
## Задача 3

Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
docker service ls
```
Решение 
```
[centos@node01 ~]$ sudo -i
[root@node01 ~]# docker service ls
ID             NAME                                MODE         REPLICAS   IMAGE                                          PORTS
nsxpni4dafyx   swarm_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0
x9q1btpb2q85   swarm_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
iv09h5tqw8h3   swarm_monitoring_cadvisor           global       6/6        google/cadvisor:latest
lqxg4zu96haj   swarm_monitoring_dockerd-exporter   global       6/6        stefanprodan/caddy:latest
lk77e7a9yjgb   swarm_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4
t0h6qo0telu7   swarm_monitoring_node-exporter      global       6/6        stefanprodan/swarmprom-node-exporter:v0.16.0
n7gp131whwmj   swarm_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0
sob9wppuxr9h   swarm_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0

[root@node01 ~]# docker stack ls
NAME               SERVICES   ORCHESTRATOR
swarm_monitoring   8          Swarm

[centos@node04 ~]$ sudo -i
[root@node04 ~]# shutdown -r now
Connection to 51.250.93.131 closed by remote host.
Connection to 51.250.93.131 closed.

```

## Задача 4 (*)

Выполнить на лидере Docker Swarm кластера команду (указанную ниже) и дать письменное описание её функционала, что она делает и зачем она нужна:
```
# см.документацию: https://docs.docker.com/engine/swarm/swarm_manager_locking/
docker swarm update --autolock=true
```

Решение:

```
[root@node01 ~]# docker swarm update --autolock=true
Swarm updated.
To unlock a swarm manager after it restarts, run the `docker swarm unlock`
command and provide the following key:

    SWMKEY-1-9DcsMLzEIYbubusJsh321TzMpoe0cF1AK4oRFNQmtoY

Please remember to store this key in a password manager, since without it you
will not be able to restart the manager.

Lock your swarm to protect its encryption key
Estimated reading time: 5 minutes
```

- нода стала недоступна
```
[root@node02 ~]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
095wn8utbs9oqx9zsoo0ucwto     node01.netology.yc   Ready     Active         Unreachable      20.10.17
mknbcqfn9sreylpaqo8ibt3zd *   node02.netology.yc   Ready     Active         Leader           20.10.17
04amqqcbdjvlqv1x4n79bscq9     node03.netology.yc   Ready     Active         Reachable        20.10.17
ve0o4p0fc5bfvj93sqrrcewl3     node04.netology.yc   Ready     Active                          20.10.17
qjl55c02w3a1qv99k8m84jlpf     node05.netology.yc   Ready     Active                          20.10.17
yk9o4qt6d41ksvxdhhwxrfhk3     node06.netology.yc   Ready     Active                          20.10.17
```

- после снятия блокировки node1 стала доступна
```
[root@node01 ~]# docker swarm unlock
Please enter unlock key:

[root@node02 ~]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
095wn8utbs9oqx9zsoo0ucwto     node01.netology.yc   Ready     Active         Reachable        20.10.17
mknbcqfn9sreylpaqo8ibt3zd *   node02.netology.yc   Ready     Active         Leader           20.10.17
04amqqcbdjvlqv1x4n79bscq9     node03.netology.yc   Ready     Active         Reachable        20.10.17
ve0o4p0fc5bfvj93sqrrcewl3     node04.netology.yc   Ready     Active                          20.10.17
qjl55c02w3a1qv99k8m84jlpf     node05.netology.yc   Ready     Active                          20.10.17
yk9o4qt6d41ksvxdhhwxrfhk3     node06.netology.yc   Ready     Active                          20.10.17
```

Из документации:


Журналы Raft, используемые менеджерами роя, по умолчанию зашифрованы на диске. Это шифрование в состоянии покоя защищает конфигурацию и данные вашего сервиса от злоумышленников, которые получают доступ к зашифрованным журналам Raft. Одна из причин, по которой эта функция была введена, заключалась в поддержке функции секретов Docker.

При перезапуске Docker в память каждого управляющего узла загружается как ключ TLS, используемый для шифрования связи между узлами роя, так и ключ, используемый для шифрования и расшифровки журналов Raft на диске. Docker может защитить общий ключ шифрования TLS и ключ, используемый для шифрования и расшифровки журналов Raft в состоянии покоя, позволяя вам стать владельцем этих ключей и требовать ручной разблокировки ваших менеджеров. Эта функция называется автоблокировкой.```

```
