# ワークショップ演習 - Ansible Automation Controller の概要

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [Ansible Automation Controller 4.0 の新機能](#ansible-automation-controller-40-の新機能)
* [Ansible Tower がAutomation Controller に改名された理由](#ansible-tower-が-automation-controller-に改名された理由)
* [Automation Controller の対象ユーザー](#automation-controller-の対象ユーザー)
* [目的](#目的)
* [ガイド](#ガイド)
* [Ansible Automation Controller を使う理由](#ansible-automation-controller-を使う理由)
* [Ansible Automation Controller ラボ環境](#ansible-automation-controller-ラボ環境)
* [ダッシュボード](#ダッシュボード)
* [コンセプト](#コンセプト)

## Ansible Automation Controller 4.0 の新機能

Ansible Automation Platform 2 は、Red Hat の信頼できるエンタープライズテクノロジーの専門家による次回の進化版です。Ansible Automation Platform 2 リリースには、Automation Controller 4.0、改善、および名前が変更された Ansible Tower が含まれます。

コントローラーは、企業全体で自動化を定義、操作、および委譲するための標準化された方法を提供します。これにより、自動化チームが迅速にスケーリングおよび配信できる、新たなテクノロジーと強化されたアーキテクチャーが導入されました。

### Ansible Tower が Automation Controller に改名された理由

Ansible Automation Platform 2 は進化し続けるため、特定の機能は、以前は Ansible Tower と知ったものから切り離されたもの（将来のリリースで分離される）から切り離されました。これらの拡張機能および Ansible Automation Platform スイート内の全体の位置を反映させることは理にかなっています。

### Automation Controller の対象ユーザー
すべての自動化チームのメンバーが対話したり、直接的または間接的に自動化コントローラーに依存します。

* 自動化作成者は Ansible Playbook、ロール、およびモジュールを開発します。
* 自動化アーキテクトはチーム全体で自動化を促進し、IT プロセスと合理化に合わせます。
* 自動化オペレーターは、自動化プラットフォームおよびフレームワークが機能することを確認します。

これらのロールは、必ずしも人やチーム専用のものであるわけではありません。多くの組織は、複数のロールをユーザーに割り当てるか、または必要に応じて特定の自動化タスクを実行します。

Automation Operator は通常、責任に基づいて自動化コントローラーと直接対話する主要な個人です。

## 目的

以下の演習では、Red Hat Ansible Automation Controller によって提供される機能の実行を含む、Automation Controller の概要を説明します。次のようなAutomation Controller の基本を対象とします。

* ジョブテンプレート
* プロジェクト
* インベントリー
* 認証情報
* ワークフロー

## ガイド

### Ansible Automation Controller を使う理由

Automation Controller は、IT 自動化のためのエンタープライズソリューションを提供する Web ベースの UI です。

* これには、ユーザーフレンドリーなダッシュボードがあります
* 自動化、視覚的管理、監視機能を追加して Ansible を補佐
* 管理者にユーザーアクセス制御を提供
* 自動化コントローラーオブジェクトおよびコンポーネントの個別の _view_ および _edit_ パースペクティブを提供します。
* インベントリーをグラフィカルに監視し、さまざまなリソースと同期
* RESTfulAPI が利用可
* その他いろいろ...

### Ansible Automation Controller ラボ環境

このラボでは、事前設定されたラボ環境で作業します。ここでは、以下のホストにアクセスできます。

| Role                                          | Inventory name |
| --------------------------------------------- | ---------------|
| Ansible control host & automation controller  | ansible-1      |
| Managed Host 1                                | node1          |
| Managed Host 2                                | node2          |
| Managed Host 2                                | node3          |

このラボの Ansible Automation Controller は、個別にセットアップされています。作業を行うマシンへの適切なアクセス権があることを確認してください。Ansible Automation Controller は、すでにインストールされてライセンス付与が行われています。Web UI は、HTTP/HTTPS でアクセスできます。

### ダッシュボード

Automation Controller を最初に見てみましょう。指定の URL にブラウザでアクセスします。この URL は `https://student.<workshopname>.demoredhat.com` のようなもので、`workshopname` は、現在のワークショップ名に置き換えます。次に `admin` としてログインします。このパスワードは、インストラクターから渡されます。

Automation Controller のWeb UI では、次を示すグラフのあるダッシュボードが表示されます。

* 最近のジョブのアクティビティー
* 管理対象ホストの数
* 問題のあるホストのリストへのクイックポインター。

このダッシュボードには、Playbook で完了したタスクの実行に関するリアルタイムデータも表示されます。

![Ansible 自動コントロ−ラーダッシュボード](images/controller_dashboard.jpg)

### コンセプト

Ansible Automation Controller を使用する方法を詳しく説明する前に、いくつかの概念と命名規則について理解しておく必要があります。

#### プロジェクト

プロジェクトは、Ansible Automation Controller にある Ansible Playbook の論理的なコレクションです。これらの Playbook は、Ansible インスタンス、またはAutomation Controller でサポートされているソースコードバージョン管理システムにあります。

#### インベントリー

インベントリーは、ジョブを起動できるホストのコレクションです (Ansible インベントリーファイルと同様)。インベントリーはグループに分類され、それらのグループには実際のホストが含まれます。グループは、ホスト名を Automation Controller に入力して手動で取得することも、Ansible Automation Controller のサポートされるクラウドプロバイダーや動的インベントリースクリプトから取得することもできます。

#### 認証情報

認証情報は、ジョブをマシンに対して起動したり、インベントリーソースと同期したり、プロジェクトのコンテンツをバージョン管理システムからインポートしたりする際の認証にAutomation Controller に使用されます。認証情報は、設定で見つかります。

Automation Controller の認証情報は、Automation Controller にインポートおよび暗号化されて保存されます。コマンドラインにおいてプレーンテキストで取得することはできません。実際に認証情報をユーザーに公開することなく、これらの認証情報を使用する機能をユーザーとチームに付与できます。

#### テンプレート

ジョブテンプレートは、Ansible ジョブを実行するための定義であり、パラメーターセットでもあります。ジョブテンプレートは同じジョブを何度も実行する場合に便利です。また、ジョブテンプレートは Ansible playbook コンテンツの再利用およびチーム間のコラボレーションを促進します。ジョブを実行するには、Automation Controller ではジョブテンプレートを先に作成する必要があります。

#### ジョブ

ジョブは、Ansible Playbook をホストのインベントリーに対して起動するAutomation Controller のインスタンスです。

---
**ナビゲーション**
<br>
[前の演習](../1.7-role/README.ja.md) - [次の演習](../2.2-cred/README.ja.md)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-tower-exercises)
