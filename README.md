# Istio勉強会
- [Istio勉強会](#istio勉強会)
  - [要求する環境（Requirement）](#要求する環境requirement)
  - [環境構築（terraform_or_istioctl）](#環境構築terraform_or_istioctl)
    - [terraform（Provision Tool）](#terraformprovision-tool)
    - [istioctl（CLI）](#istioctlcli)
      - [kiali（可視化コンポーネント）](#kiali可視化コンポーネント)
      - [aws-istio-example](#aws-istio-example)
  - [Istio](#istio)
  - [Deployment](#deployment)
    - [Architecture](#architecture)
    - [Deployment Models](#deployment-models)
      - [Cluster models](#cluster-models)
      - [Network models](#network-models)
      - [Control plane models](#control-plane-models)
      - [Identity and trust models](#identity-and-trust-models)
      - [Tenancy models](#tenancy-models)
    - [Performance and Scalability](#performance-and-scalability)
    - [Pods and Services](#pods-and-services)
  - [メモ](#メモ)
    - [技術用語](#技術用語)
    - [Namespaceに後付けで，Sidecarを導入する際](#namespaceに後付けでsidecarを導入する際)

## 要求する環境（Requirement）
- istio関連のバージョン
  - client version: 1.6.4
  - control plane version: 1.6.4
  - data plane version: 1.6.4

## 環境構築（terraform_or_istioctl）
### terraform（Provision Tool）
- `cd ./eks/terraform`
- `terraform init`
- `terraform plan`
- `terraform apply`
### istioctl（CLI）
- `istioctl install --set profile=demo`
#### kiali（可視化コンポーネント）
- `istioctl dashboard kiali`
  - svcとポートフォワードし，ブラウザにログイン画面が出る
- user/pass: `admin/admin`
#### aws-istio-example
- `k apply -f ./istio-on-amazon-eks-master`

## Istio
## Deployment
### Architecture
- 2層に別れている
  - Data Plan（Envoy Proxy）
    - トラフィックの制御
    - Podの前段に，Envoyをインジェクト（サイドカーパターン）
  - Control Plan（istiod）
    - サービスディスカバリ（識別子 ⇄ 実体 の参照解決）
    - コンフィグ・証明書管理

---
### Deployment Models
#### Cluster models
- 単一クラスタでも，複数クラスタでも展開可能
- クラスタが落ちても別のクラスタにトラフィックを流してくれる
#### Network models
- 複数のネットワークを跨ぐ構成で，サブネット間の疎通性がない場合は，各ネットワークにistio-gatewayを配置してコントロールプレーンと繋げる
#### Control plane models
- コントロールプレーンもHA構成可能
#### Identity and trust models
- ここは触ってみないとあんまり理解できない
#### Tenancy models
- Namespace，Clusterレベルで通信を隔離できる

---
### Performance and Scalability
- 特になし

---
### Pods and Services
- 特になし

## メモ
### 技術用語
- ingress (inbound monitoring)
- egress (outbound monitoring)
### Namespaceに後付けで，Sidecarを導入する際
- Namespaceにラベル付け
  - `k label ns {your namespace} istio-injection=enabled`
- 一度，Podを消去し再起動
  - `k delete pods {pod name}`