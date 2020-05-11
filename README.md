# lemniscata
Repositório para Projeto Integrador DevOps da Academia Neon

# Problema
* Executar aplicativo [digitalhouse-devops-app](https://github.com/nkgushiken/digitalhouse-devops-app) que faz o upload de uma arquivo de imagem em um bucket s3 da AWS.

# Solução
* Criar um ambiente para deploy da aplicação:
    1) Ambiente na AWS
        * Máquina EC2 com Jenkins
        * Ambiente de deploy
            * Máquina EC2: homologação
            * Máquina EC2: produção
    2) Dois buckets S3
        * dh-lemniscata-devops-homolog
        * dh-lemniscata-devops-prod
    3) Repositório ECR

    ## Playbooks
    * `ec2-up/playbooks/config-vm.yml`
        Configura o desktop local
    * `ec2-up/playbooks/aws_provisioning.yml`
        Construção das máquinas EC2 na AWS
    * `ec2-up/playbooks/config-vm-aws.yml`
        Configura a VM
    * `ec2-up/playbooks/docker.yml`
        Instala o docker nas três instâncias
    * `ec2-up/playbooks/jenkins.yml`, `ec2-up/playbooks/ansible.yml`
        Instala Jenkins e Ansible na instância de gerenciamento
    * `ec2-up/playbooks/s3_up`
        Constrói os buckets S3 e define as políticas
    
    ## App homologação
    * Deploy do Jenkinsfile do [repositório lemniscata: branch homolog](https://github.com/nkgushiken/lemniscata/tree/homolog)
    ## App produção
    * Deploy do Jenkinsfile do [repositório digitalhouse-devops-app: branch master](https://github.com/nkgushiken/digitalhouse-devops-app)

# Evidências
## Pipeline
* [Pipeline homologação](https://github.com/nkgushiken/lemniscata/blob/master/output/consoleText_pipeline_homolog.txt)
* [Pipeline produção](https://github.com/nkgushiken/lemniscata/blob/master/output/consoleText_pipeline_prod.txt)
## Healthcheck
* **Homologação**
![Healthcheck homologação](https://github.com/nkgushiken/lemniscata/blob/master/output/healthcheck_homolog_print.png)
* **Produção**
![Healthcheck produção](https://github.com/nkgushiken/lemniscata/blob/master/output/healthcheck_prod_print.png)
## Ambientes
* **Homologação**
![Ambiente homologação](https://github.com/nkgushiken/lemniscata/blob/master/output/env_homolog_print.png)
* **Produção**
![Ambiente produção](https://github.com/nkgushiken/lemniscata/blob/master/output/env_prod_print.png)
## Buckets
* **Homologação**
![Bucket homologação](https://github.com/nkgushiken/lemniscata/blob/master/output/bucket_homolog_print.png)
* **Produção**
![Bucket produção](https://github.com/nkgushiken/lemniscata/blob/master/output/bucket_prod_print.png)

