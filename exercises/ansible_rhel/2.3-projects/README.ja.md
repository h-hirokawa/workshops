# ワークショップ演習 - プロジェクトとジョブテンプレート

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [目的](#目的)
* [ガイド](#ガイド)
* [Git リポジトリーのセットアップ](#git-リポジトリーのセットアップ)
* [プロジェクトの作成](#プロジェクトの作成)
* [ジョブテンプレートの作成とジョブの実行](#ジョブテンプレートの作成とジョブの実行)
* [チャレンジラボ: 結果のチェック](#チャレンジラボ-結果のチェック)

## 目的

Ansible Automation Controller **プロジェクト** は、AnsiblePlaybook の論理的なコレクションです。Playbook は、Git、Subversion などのAutomation Controller がサポートするソースコード管理 (SCM) システムに配置することで管理できます。

この演習では、以下について説明します。

* Ansible Automation Controller プロジェクトの概要と利用
* Git リポジトリーに保存されている AnsiblePlaybook の使用
* Ansible ジョブテンプレートの作成と使用

## ガイド

### Git リポジトリーのセットアップ

このデモでは、Git リポジトリーに保存されている Playbook を使用します。

[https://github.com/ansible/workshop-examples](https://github.com/ansible/workshop-examples)

Apache Web サーバーをインストールする Playbook が既に **rhel/apache** ディレクトリーにコミットされている (`apache_install.yml`):

```yaml
---
- name: Apache server installed
  hosts: web

  tasks:
  - name: latest Apache version installed
    yum:
      name: httpd
      state: latest

  - name: latest firewalld version installed
    yum:
      name: firewalld
      state: latest

  - name: firewalld enabled and running
    service:
      name: firewalld
      enabled: true
      state: started

  - name: firewalld permits http service
    firewalld:
      service: http
      permanent: true
      state: enabled
      immediate: yes

  - name: Apache enabled and running
    service:
      name: httpd
      enabled: true
      state: started
```

> **ヒント**
>
> 作成した Playbook の違いをメモしてください。最も重要なのは、`become` がなく、`hosts` が `all` に設定されていることです。

Automation Controller で **Source Control Management (SCM)** として、このレポジトリーを設定して使用するには、このレポジトリーを使用する **プロジェクト** を作成する必要があります。

### プロジェクトの作成

* **Automation Execution → プロジェクト** に移動します。フォームで **プロジェクトの作成** ボタンをクリックし、フォームを記入します。

 <table>
   <tr>
     <th>パラメーター</th>
     <th>値</th>
   </tr>
   <tr>
     <td>名前</td>
     <td>Workshop Project</td>
   </tr>
   <tr>
     <td>組織</td>
     <td>Default</td>
   </tr>
   <tr>
     <td>実行環境</td>
     <td>Default execution environment</td>
   </tr>
   <tr>
     <td>ソースコントロールのタイプ</td>
     <td>Git</td>
   </tr>
 </table>

プロジェクト構成に URL を入力します。

 <table>
   <tr>
     <th>パラメーター</th>
     <th>値</th>
   </tr>
   <tr>
     <td>ソースコントロールの URL</td>
     <td><code>https://github.com/ansible/workshop-examples.git</code></td>
   </tr>
   <tr>
     <td>オプション</td>
     <td>「Clean」、「削除」、「Update revision on launch」にチェック (ジョブ実行の都度、新規で最新のプロジェクトを取得するため)</td>
   </tr>
 </table>

* **保存** をクリックします。


新しいプロジェクトは、作成後に自動的に同期されます。ただし、これを手動で行うこともできます。**プロジェクト** ビューに移動し、プロジェクトの右側にある円形の矢印 *プロジェクトの同期** アイコンをクリックして、プロジェクトを Git リポジトリーと再度同期します。

同期ジョブを開始した後、**ジョブ** ビューに移動します。Git リポジトリーを更新するための新しいジョブがあります。

### ジョブテンプレートの作成とジョブの実行

ジョブテンプレートは、Ansible ジョブを実行するための定義とパラメーターのセットです。ジョブテンプレートは、同じジョブを何度も実行するのに役立ちます。したがって、Automation Controller から Ansible **ジョブ**を実行する前に、まとめる **ジョブテンプレート** を作成する必要があります。

* **インベントリー**: ジョブが実行するホスト

* **認証情報** ホストへのログインに必要な認証情報

* **プロジェクト**: Playbook の場所

* **Playbook** 使用する Playbook

実際にやってみましょう。**リソース -> テンプレート** ビューに移動して、**テンプレートの作成** ボタンをクリックし、**ジョブテンプレートの作成** を選択します。

> **ヒント**
>
> フィールドへの記入時に、選択式のオプションを設定するには虫眼鏡をクリックします。

 <table>
   <tr>
     <th>パラメーター</th>
     <th>値</th>
   </tr>
   <tr>
     <td>名前</td>
     <td>Install Apache</td>
   </tr>
   <tr>
     <td>ジョブタイプ</td>
     <td>実行</td>
   </tr>
   <tr>
     <td>インベントリー</td>
     <td>Workshop Inventory</td>
   </tr>
   <tr>
     <td>プロジェクト</td>
     <td>Workshop Project</td>
   </tr>
   <tr>
     <td>Playbook</td>
     <td><code>rhel/apache/apache_install.yml</code></td>
   </tr>
   <tr>
     <td>実行環境</td>
     <td>Default execution environment</td>
   </tr>
   <tr>
     <td>認証情報</td>
     <td>Workshop Credential</td>
   </tr>
   <tr>
     <td>制限</td>
     <td>web</td>
   </tr>
   <tr>
     <td>オプション</td>
     <td>「権限昇格」をチェック (root権限での実行が必要なため)</td>
   </tr>
 </table>

* **ジョブテンプレートの作成** をクリックします。

**テンプレートの起動** ボタンを直接クリックするか、ジョブテンプレートの一覧でロケットをクリックすると、ジョブを開始できます。ジョブテンプレートを起動すると、自動的にジョブの概要が表示され、Playbook の実行をリアルタイムで追跡できます。

ジョブの詳細
![job details](images/job_details.png)

ジョブの実行
![job_run](images/job_run.png)

これには時間がかかる場合があるため、提供されているすべての詳細を詳しく調べてください。

* インベントリー、プロジェクト、認証情報、Playbook などのジョブテンプレートのすべての詳細が表示されます。

* さらに、Playbook の実際のリビジョンがここに記録されます。これにより、後でジョブの実行を分析しやすくなります。

* また、開始時間と終了時間の実行時間が記録されるため、ジョブの実行が実際にどのくらいの時間であったかがわかります。

* **出力** を選択すると、Playbook の実行の出力が表示されます。タスクの下のノードをクリックして、各ノードの各タスクの詳細情報が表示されていることを確認します。

ジョブが終了したら、メインの **ジョブ** ビューに移動します。すべてのジョブがここに一覧表示されます。Playbook が実行される前に「ソースコントロールの更新」が実行されていたことがわかります。これは、**プロジェクト** で構成した Git リポジトリの起動時アップデートです。

### チャレンジラボ: 結果のチェック

小チャレンジ:

* 両方のホストでアドホックコマンドを使用して、Apache がインストールされ、実行されていることを確認します。

必要なすべての手順をすでに完了しているので、これを自分で試してください。

> **ヒント**
>
> `systemctl status httpd` が使用可能


> **警告**
>
> **回答を以下に示します**

* **Automation Execution** → **Insrastructure** → **インベントリー** → **Workshop Inventory** に移動します。

* **ホスト** 一覧で、`node1`、`node2`、`node3` を選択して、**Run command** をクリックします。

**詳細** ウィンドウで、**Module**に `command` 、**Arguments**に `systemctl status httpd` を 指定して、**Next** をクリックします。

**実行環境** ウィンドウで **Default execution environment** を選択し、**Next** をクリックします。

**認証情報**ウィンドウで、**Workshop Credential** を選択し、**Next** をクリックし、次のウィンドウで **Finish** を選択します。

> **ヒント**
>
> 結果の出力は、コマンドが完了すると表示されます。

---
**ナビゲーション**
<br>
[前の演習](../2.2-cred/README.ja.md) - [次の演習](../2.4-surveys/README.ja.md)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-tower-exercises)
