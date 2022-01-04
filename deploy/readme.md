一.证书配置
mkdir /tmp/deploy
openssl req -new -newkey rsa:2048 -days 3650 -nodes -x509 -keyout node_exporter.key -out node_exporter.crt -subj "/C=CN/ST=Beijing/L=Beijing/O=test.cn/CN=localhost"

mv node_exporter  /tmp/deploy

cat config.yaml
tls_server_config:
cert_file: node_exporter.crt
key_file: node_exporter.key


./node_exporter --web.config=config.yaml

curl -k https://localhost:9100/metrics

prometheus配置
- job_name: 'node_exporter'
  scheme: https
  tls_config:
  ca_file: node_exporter.crt
  static_configs:
    - targets: ['localhost:9100']

二.添加 Basic Auth
htpasswd -nBC 12 '' | tr -d ':\n'

cat config.yaml
tls_server_config:
cert_file: node_exporter.crt
key_file: node_exporter.key
basic_auth_users:
# 当前设置的用户名为 prometheus ， 可以设置多个
prometheus: 123456

./node_exporter --web.config=config.yaml

curl  -Ik https://localhost:9100/metrics

- job_name: 'node_exporter'
  scheme: https
  tls_config:
  ca_file: node_exporter.crt
  basic_auth:
  username: prometheus
  password: 123456  
  static_configs:
    - targets: ['localhost:9100']
  
  
 