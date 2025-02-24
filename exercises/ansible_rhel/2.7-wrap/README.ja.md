# ワークショップ演習 - まとめ

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [目的](#目的)
* [ガイド](#ガイド)
  * [ステージの設定](#ステージの設定)
  * [Git リポジトリー](#git-リポジトリー)
  * [インベントリーの準備](#インベントリーの準備)
  * [テンプレートの作成](#テンプレートの作成)
  * [結果の確認](#結果の確認)
  * [Survey の追加](#survey-の追加)
  * [ソリューション](#ソリューション)
* [終わり](#終わり)

## 目的

これは、これまで学んだことの復習を目的とした最後のチャレンジです。

## ガイド

### ステージの設定

運営チームとアプリケーション開発チームは、Ansible Automation Controller の機能を気に入っています。実際の環境で使用するために、要件を次にまとめました。

* すべてのウェブサーバー (`node1`、`node2`、`node3`) を 1 つのグループに入れる必要があります

* Web サーバーは開発目的または本番環境で使用できるため、それに応じて「stage dev」または「stage prod」としてフラグを立てる方法が必要です。

  * 現在、`node1` と `node3` が開発システムとして使用され、`node2` が稼働環境となっています。

* もちろん、世界的に有名なアプリケーション「index.html」の内容は、開発段階と製品段階で異なります。

  * ページには環境を示したタイトルが表示されます。
  * コンテンツフィールドがあります。

* コンテンツライター `wweb` には、dev サーバーと prod サーバーのコンテンツを変更するための Survey にアクセスできる必要があります。

### Git リポジトリー

すべてのコードはすでに配置されています。これはAutomation Controller ラボですから。[https://github.com/ansible/workshop-examples](https://github.com/ansible/workshop-examples) にある **Workshop Project** git リポジトリを確認してください。そこに Playbook `webcontent.yml` があります。これは、ロール `role_webcontent` を呼び出します。

以前の Apache インストールのロールと比較すると、大きな違いがあります。現在、2つのバージョンの `index.html` テンプレート、およびソースファイル名の一部として変数を持つテンプレートファイルをデプロイするタスクがあります。

`dev_index.html.j2`

<!-- {% raw %} -->

```html
<body>
<h1>This is a development webserver, have fun!</h1>
{{ dev_content }}
</body>
```

<!-- {% endraw %} -->

`prod_index.html.j2`

<!-- {% raw %} -->

```html
<body>
<h1>This is a production webserver, take care!</h1>
{{ prod_content }}
</body>
```

<!-- {% endraw %} -->

`main.yml`

<!-- {% raw %} -->

```yaml
[...]
- name: Deploy index.html from template
  template:
    src: "{{ stage }}_index.html.j2"
    dest: /var/www/html/index.html
  notify: apache-restart
```

<!-- {% endraw %} -->

### インベントリーの準備

このラボでは Ansible Automation Controllerを使用して、上記のデプロイを行なっていきます。

**Automation Execution** -> **Infrastructure** -> **インベントリー** 内で、「Workshop Inventory」を選択します。

**グループ** タブで、**グループの作成** ボタンをクリックして、`Webserver` という名前のグループを作成します。

`Webserver` グループの **詳細** タブで、**Edit group** をクリックします。**変数** テキストボックスでは、`stage` という値を持つ変数 `dev` を定義し、**保存** をクリックします。

```yaml
---
stage: dev
```

`Webserver` グループの **詳細** から **ホスト** タブをクリックし、**Add exiting host** ボタンをクリックします。`Webserver` インベントリーに含まれるホストとして、`node1`、`node2`、および `node3` を選択します。


**Automation Execution** -> **Infrastructure** -> **インベントリー** 内で、`Workshop Inventory` を選択し、**ホスト** タブをクリックして、`node2` をクリックします。`編集` をクリックし、**変数** ウィンドウで `stage: prod` 変数を追加します。これは、Playbook の実行時に変数にアクセスする方法により、インベントリー変数を上書きします。


**変数** テキストボックスで、`stage` という値を持つ `prod` というラベルが付いた変数を定義して、保存します。

```yaml
---
ansible_host: <IP_of_node2>
stage: prod
```
> **ヒント**
>
> `ansible_host` の行は削除しないようにしてください。

### テンプレートの作成

次に新規ジョブテンプレートをクリックし、以下のように作成します。

  <table>
    <tr>
      <th>パラメーター</th>
      <th>値</th>
    </tr>
    <tr>
      <td>名前</td>
      <td>Create Web Content</td>
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
      <td>rhel/apache/webcontent.yml</td>
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
      <td>Extra variables</td>
      <td>---<br>dev_content: "default dev content"<br>prod_content: "default prod content"</td>
    </tr>
    <tr>
      <td>オプション</td>
      <td>権限昇格</td>
    </tr>
  </table>

**保存** をクリックします。

**起動** ボタンをクリックしてテンプレートを実行します。


### 結果の確認

今回は、Ansibleコマンドラインを使って結果を確認しましょう。各ノードから Web コンテンツを取得するために uri モジュールを実行し、Ansible Playbook (`check_url.yml`) を作成します。

> **ヒント**
>
> インベントリーグループの各ノードにアクセスするために、URL に `ansible_host` 変数を使用しています。

<!-- {% raw %} -->

```yaml
---
- name: Check URL results
  hosts: web

  tasks:
    - name: Check that you can connect (GET) to a page and it returns a status 200
      uri:
        url: "http://{{ ansible_host }}"
        return_content: yes
      register: content

    - debug:
       var: content.content
```

```bash
[student@ansible-1 ~]$ ansible-navigator run check_url.yml -m stdout
```

出力のスニペット:

```bash
TASK [debug] *******************************************************************
ok: [node1] => {
    "content.content": "<body>\n<h1>This is a development webserver, have fun!</h1>\ndev wweb</body>\n"
}
ok: [node2] => {
    "content.content": "<body>\n<h1>This is a production webserver, take care!</h1>\nprod wweb</body>\n"
}
ok: [node3] => {
    "content.content": "<body>\n<h1>This is a development webserver, have fun!</h1>\ndev wweb</body>\n"
}
```

<!-- {% endraw %} -->

### Survey の追加

* Survey をテンプレートに追加して、変数 `dev_content` および `prod_content` の変更を可能にします。
* テンプレート中の **Survey** タブをクリックして、Questionを追加します。
* 以下の情報を入力してください。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>Question</td>
    <td>dev_contentの値は?</td>
  </tr>
  <tr>
    <td>Answer variable name</td>
    <td>dev_content</td>
  </tr>
  <tr>
    <td>Answer type</td>
    <td>Text</td>
  </tr>
</table>

同じように、2 番目の質問を追加します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>Question</td>
    <td>prod_contentの値は?</td>
  </tr>
  <tr>
    <td>Answer variable name</td>
    <td>prod_content</td>
  </tr>
  <tr>
    <td>Answer type</td>
    <td>Text</td>
  </tr>
</table>

* トグルをクリックして Survey enabled に切り替えます。

次にRoleを割り当てて、テンプレート **Create Web Content** を `wweb` が実行できるようにします。

* テンプレートの **User Access** タブから `wweb` にテンプレート実行ロール(JobTemplate Execute)を割り当て、テンプレートを実行できるようにします。
* ユーザー `wweb` として survey を実行します。
  * Ansible Automation Controller のユーザー `admin` からログアウトします。
  * `wweb` としてログインし、**Create Web Content** テンプレートを実行します。

再び、オートメーションコントローラーのホストから再度結果を確認します。

<!-- {% raw %} -->


```bash
[student@ansible-1 ~]$ ansible-navigator run check_url.yml -m stdout
```

<!-- {% endraw %} -->

## 終わり

おめでとうございます。ラボを完了しました。我々がラボの構築を楽しめたように、Ansible Automation Controller を楽しんでいただければ光栄です。

----
**ナビゲーション**
<br>
[前の演習](../2.6-workflows/README.ja.md)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-automation-controller-exercises)
