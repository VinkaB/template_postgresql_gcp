pipeline {
  agent any

  environment {
    PAIS = 'CL'
    DB_BACKUP_ENABLED = 'true'
    DB_SERVICE_PROVIDER = 'GCP - Cloud SQL'
    DB_ENGINE = 'PostgreSQL'
    DB_RESOURCE_LABELS = 'test'
    DB_LABELS_TAGS = 'test'
  }

  parameters {
    // GCP
    string(name: 'PROJECT_ID', defaultValue: '', description: 'ID del proyecto')
    string(name: 'REGION', defaultValue: '', description: 'Región')
    string(name: 'ZONE', defaultValue: '', description: 'Zona')
    choice(name: 'ENVIRONMENT', choices: ['beta', 'pre prod', 'productivo'], description: 'Ambiente')
    string(name: 'DB_INSTANCE_NAME', defaultValue: '', description: 'Nombre instancia')
    string(name: 'DB_INSTANCE_ID', defaultValue: '', description: 'ID instancia')
    choice(name: 'DB_AVAILABILITY_TYPE', choices: ['regional', 'single zone'], description: 'Disponibilidad')
    choice(name: 'DB_VERSION', choices: ['POSTGRES_15', 'POSTGRES_14'], description: 'Versión PostgreSQL')
    string(name: 'DB_TIME_ZONE', defaultValue: '', description: 'Zona horaria')
    choice(name: 'MACHINE_TYPE', choices: ['db-custom-4-16384', 'standard'], description: 'Máquina')
    string(name: 'DB_MAX_CONNECTIONS', defaultValue: '', description: 'Máx conexiones')
    string(name: 'DB_STORAGE_SIZE', defaultValue: '', description: 'Almacenamiento (GB)')
    choice(name: 'DB_STORAGE_AUTO_RESIZE', choices: ['true','false'], description: 'Auto-resize')
    choice(name: 'DB_STORAGE_TYPE', choices: ['SSD','HDD'], description: 'Tipo de disco')
    string(name: 'DB_USERNAME', defaultValue: 'postgres', description: 'Usuario')
    string(name: 'DB_PASSWORD', defaultValue: 'password', description: 'Password')

    // REDES
    string(name: 'DB_VPC_NETWORK', defaultValue: '', description: 'VPC')
    string(name: 'DB_SUBNET', defaultValue: '', description: 'Subred')
    choice(name: 'DB_PUBLIC_ACCESS_ENABLED', choices: ['false','true'], description: 'Acceso público')
    choice(name: 'DB_PRIVATE_IP_ENABLED', choices: ['true','false'], description: 'IP privada')
    string(name: 'DB_IP_RANGE_ALLOWED', defaultValue: '', description: 'Rangos permitidos (CIDR)')
    choice(name: 'DB_SSL_ENABLED', choices: ['true','false'], description: 'SSL')

    // SEGURIDAD Y OPERACIÓN
    string(name: 'DB_BACKUP_START_TIME', defaultValue: '', description: 'Hora inicio backup (HH:MM)')
    string(name: 'DB_BACKUP_RETENTION_DAYS', defaultValue: '', description: 'Retención (días)')
    string(name: 'DB_MAINTENANCE_WINDOW_DAY', defaultValue: '', description: 'Día mantención')
    string(name: 'DB_MAINTENANCE_WINDOW_HOUR', defaultValue: '', description: 'Hora mantención')
    choice(name: 'DB_MONITORING_ENABLED', choices: ['true','false'], description: 'Monitoring')
    choice(name: 'DB_AUDIT_LOGS_ENABLED', choices: ['true','false'], description: 'Audit logs')
    string(name: 'CREDENTIAL_FILE', defaultValue: '', description: 'Ruta credenciales (JSON)')
    string(name: 'DB_IAM_ROLE', defaultValue: '', description: 'IAM Role')
    choice(name: 'DB_DELETION_PROTECTION', choices: ['true','false'], description: 'Protección borrado')
    choice(name: 'CHECK_DELETE', choices: ['true','false'], description: 'Check delete')
    choice(name: 'DB_ENCRYPTION_ENABLED', choices: ['true','false'], description: 'Encripción')

    // REPLICA / FAILOVER
    choice(name: 'DB_FAILOVER_REPLICA_ENABLED', choices: ['false','true'], description: 'Failover replica')
    choice(name: 'DB_READ_REPLICA_ENABLED', choices: ['false','true'], description: 'Read replica')
  }

  stages {
    stage('Imprimir variables') {
      steps {
        script {
          def ocultas = [
            PAIS: env.PAIS,
            DB_BACKUP_ENABLED: env.DB_BACKUP_ENABLED,
            DB_SERVICE_PROVIDER: env.DB_SERVICE_PROVIDER,
            DB_ENGINE: env.DB_ENGINE,
            DB_RESOURCE_LABELS: env.DB_RESOURCE_LABELS,
            DB_LABELS_TAGS: env.DB_LABELS_TAGS
          ]

          def visibles = [
            // GCP
            PROJECT_ID: params.PROJECT_ID,
            REGION: params.REGION,
            ZONE: params.ZONE,
            ENVIRONMENT: params.ENVIRONMENT,
            DB_INSTANCE_NAME: params.DB_INSTANCE_NAME,
            DB_INSTANCE_ID: params.DB_INSTANCE_ID,
            DB_AVAILABILITY_TYPE: params.DB_AVAILABILITY_TYPE,
            DB_VERSION: params.DB_VERSION,
            DB_TIME_ZONE: params.DB_TIME_ZONE,
            MACHINE_TYPE: params.MACHINE_TYPE,
            DB_MAX_CONNECTIONS: params.DB_MAX_CONNECTIONS,
            DB_STORAGE_SIZE: params.DB_STORAGE_SIZE,
            DB_STORAGE_AUTO_RESIZE: params.DB_STORAGE_AUTO_RESIZE,
            DB_STORAGE_TYPE: params.DB_STORAGE_TYPE,
            DB_USERNAME: params.DB_USERNAME,
            DB_PASSWORD: params.DB_PASSWORD,

            // REDES
            DB_VPC_NETWORK: params.DB_VPC_NETWORK,
            DB_SUBNET: params.DB_SUBNET,
            DB_PUBLIC_ACCESS_ENABLED: params.DB_PUBLIC_ACCESS_ENABLED,
            DB_PRIVATE_IP_ENABLED: params.DB_PRIVATE_IP_ENABLED,
            DB_IP_RANGE_ALLOWED: params.DB_IP_RANGE_ALLOWED,
            DB_SSL_ENABLED: params.DB_SSL_ENABLED,

            // SEGURIDAD Y OPERACIÓN
            DB_BACKUP_START_TIME: params.DB_BACKUP_START_TIME,
            DB_BACKUP_RETENTION_DAYS: params.DB_BACKUP_RETENTION_DAYS,
            DB_MAINTENANCE_WINDOW_DAY: params.DB_MAINTENANCE_WINDOW_DAY,
            DB_MAINTENANCE_WINDOW_HOUR: params.DB_MAINTENANCE_WINDOW_HOUR,
            DB_MONITORING_ENABLED: params.DB_MONITORING_ENABLED,
            DB_AUDIT_LOGS_ENABLED: params.DB_AUDIT_LOGS_ENABLED,
            CREDENTIAL_FILE: params.CREDENTIAL_FILE,
            DB_IAM_ROLE: params.DB_IAM_ROLE,
            DB_DELETION_PROTECTION: params.DB_DELETION_PROTECTION,
            CHECK_DELETE: params.CHECK_DELETE,
            DB_ENCRYPTION_ENABLED: params.DB_ENCRYPTION_ENABLED,

            // REPLICA / FAILOVER
            DB_FAILOVER_REPLICA_ENABLED: params.DB_FAILOVER_REPLICA_ENABLED,
            DB_READ_REPLICA_ENABLED: params.DB_READ_REPLICA_ENABLED
          ]

          echo '======== DEFAULT ========'
          ocultas.each { k, v -> echo "${k}: ${v}" }

          echo '======== VISIBLES ========'
          visibles.each { k, v -> echo "${k}: ${v}" }
        }
      }
    }
  }

  post {
    success { echo 'Pipeline ejecutado correctamente.' }
    failure { echo 'Error al ejecutar el pipeline.' }
  }
}
