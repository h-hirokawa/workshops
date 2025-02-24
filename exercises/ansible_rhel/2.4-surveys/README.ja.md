# 演習 - Survey

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [目的](#目的)
* [ガイド](#ガイド)
* [Apache-configuration ロール](#apache-configuration-ロール)
* [Survey を持つテンプレートの作成](#survey-を持つテンプレートの作成)
  * [テンプレートの作成](#テンプレートの作成)
  * [Survey の追加](#survey-の追加)
* [テンプレートの起動](#テンプレートの起動)

## 目的

Ansible Automation Controllerの [Survey 機能](https://docs.ansible.com/automation-controller/latest/html/userguide/job_templates.html#surveys) の使用のデモンストレーションを行います。Survey は、「Prompt for Extra Variables (追加変数のプロンプト)」と同様に Playbook 実行時の追加変数を設定しますが、ユーザーが使いやすい質問と回答を使ってこれを実行します。また、Survey ではユーザー入力を検証することもできます。

## ガイド

先ほど実行したジョブではすべてのホストに Apache をインストールしました。次に、これに拡張を行っていきます。

* jinja2 テンプレートを持つ適切なロールを使用して、`index.html`ファイルをデプロイします。

* **ジョブテンプレート** を作成し、Survey で `index.html` の値を設定できるようにします。

* **ジョブテンプレート** を起動します。

さらに、この演習のために Apache の設定が適切に設定されていることを確認する役割もあります。

> **ヒント**
>
> この Survey 機能では、シンプルなデータ入力の機構を提供しますが、第三者による承認や動的データに基づいたクエリー、ネストされたメニューには対応していません。

### Apache設定ロール

Jinja2 テンプレートを使用したロールとそれを用いるPlaybookが、Github リポジトリー [https://github.com/ansible/workshop-examples](https://github.com/ansible/workshop-examples) 内のディレクトリー `rhel/apache` に既に存在します。

 Github UI にアクセスして、コンテンツを確認します。Playbook `apache_role_install.yml` は単にロールを参照します。ロールは、`roles/role_apache` サブディレクトリーにあります。

* ロール内で、`{{…​}}` でマークされている `templates/index.html.j2` テンプレートファイルの 2 つの変数をメモします。
* また、テンプレートからファイルをデプロイする、`tasks/main.yml` のタスクを確認します。

この Playbook では、テンプレート (**src**) を管理対象ホスト上で展開することでファイル (**dest**) を作成します。

あわせてこのロールは、Apache 自体の設定も行っており。これにより、前の章で行ったすべての変更が上書きされ、ここでの例が正しく動作するようになります。

Playbook とロールは、`apache_install.yml` Playbook と同じ Github レポジシトリーにあるため、この演習用に新しいプロジェクトを構成する必要はありません。

### Survey を持つテンプレートの作成

次は、survey を含む新しいテンプレートを作成します。

#### テンプレートの作成

* **Automation Execution → テンプレート** に移動し、**テンプレートの作成** ボタンをクリックして、**ジョブテンプレートの作成** を選択します。

* 次の情報を入力します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>名前</td>
    <td>Create index.html</td>
  </tr>
  <tr>
    <td>ジョブタイプ</td>
    <td>実行</td>
  </tr>
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
   <td><code>rhel/apache/apache_role_install.yml</code></td>
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
    <td>「権限昇格」をチェック</td>
  </tr>
</table>

* **ジョブテンプレートの作成** をクリックします。

> **警告**
>
> **まだテンプレートは実行しないでください。**

#### Survey の追加

* テンプレートで **Survey** タブをクリックして、**Create survey question** ボタンをクリックします。

* 次の情報を入力します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>Question</td>
    <td>First Line</td>
  </tr>
  <tr>
    <td>Answer variable name</td>
    <td>first_line</td>
  </tr>
  <tr>
    <td>Answer type</td>
    <td>Text</td>
  </tr>
</table>

* **Create survey question** をクリックします。

同じ方法で、2 番目の Survey を追加します。

<table>
  <tr>
    <th>Question</th>
    <th>値</th>
  </tr>
  <tr>
    <td>質問</td>
    <td>Second Line</td>
  </tr>
  <tr>
    <td>Answer variable name</td>
    <td>second_line</td>
  </tr>
  <tr>
    <td>Answer type</td>
    <td>Text</td>
  </tr>
</table>

* **Create survey question** をクリックします。

* トグルをクリックして、**Survey enabled** (Survey有効状態) に切り替えます。

### テンプレートの起動

**詳細** タブを選択し、**テンプレートの起動** ボタンをクリックしてジョブテンプレート **Create index.html** を起動します。

実際に起動する前に、Survey により、**First Line** と **Second Line** が求められます。好きなテキストを入力して、**Next** をクリックします。次のウィンドウに値が表示されます。問題がなければ、**Finish** をクリックしてジョブを実行します。

ジョブが完了したら、Apache ホームページを確認します。コントロールホストの SSH コンソールで、`node1` の以下に対して `curl` を実行します。

```bash
$ curl http://node1
<body>
<h1>Apache is running fine</h1>
<h1>This is survey field "First Line": line one</h1>
<h1>This is survey field "Second Line": line two</h1>
</body>
```

Playbook によって使用されている 2 つの変数が `index.html` ファイルの内容に反映されているかを確認してください。

---
**ナビゲーション**
<br>

{% if page.url contains 'ansible_rhel_90' %}
[Previous Exercise](../4-variables) - [Next Exercise](../../ansible_rhel_90/6-system-roles/)
{% else %}
[Previous Exercise](../2.3-projects) - [Next Exercise](../2.5-rbac)
{% endif %}
<br><br>
[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md)
