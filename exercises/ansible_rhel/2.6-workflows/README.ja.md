# ワークショップ演習 - ワークフロー

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [目的](#目的)
* [ガイド](#ガイド)
  * [ラボシナリオ](#ラボシナリオ)
  * [プロジェクトのセットアップ](#プロジェクトのセットアップ)
  * [ジョブテンプレートのセットアップ](#ジョブテンプレートのセットアップ)
  * [ワークフローのセットアップ](#ワークフローのセットアップ)
  * [ワークフローの起動](#ワークフローの起動)

## 目的

ワークフローの基本的な考え方は、複数のジョブテンプレートをリンクするというものです。インベントリー、Playbook、さらにはパーミッションを共有する場合と共有しない場合があります。リンクは条件付きにすることができます。

* ジョブテンプレート A が成功すると、その後ジョブテンプレート B が自動的に実行されます。
* ただし、失敗した場合は、ジョブテンプレート C が実行されます。

また、ワークフローは Job Templates に限定されるものではなく、プロジェクトやインベントリーの更新を含めることもできます。

これにより、Ansible Automation Controller による新たな応用として、さまざまなジョブテンプレートを相互に構築することができるようになります。たとえば、ネットワーキングチームは、独自のコンテンツを使用して、独自のGitリポジトリに、独自のインベントリを対象とした Playbook を作成します。一方、運用チームにも、独自のリポジトリー、Playbook、およびインベントリーを持っているような状況に適用できます。

このラボでは、ワークフローを設定する方法を説明します。

## ガイド

### ラボシナリオ

組織に 2 つの部門があるとします。

* `webops` という独自の Git ブランチで Playbook を開発している Web 運用チーム
* `webdev` という独自の Git ブランチで Playbook を開発しているWeb開発者チーム。

新しい Node.js サーバーをデプロイする場合は、次の 2 つのことが必要です。

#### Web 運用チーム

* `httpd`、`firewalld`、および `node.js` をインストールし、`SELinux` 設定を定義し、ファイアウォールを開き、`httpd` および `node.js` を起動する。

#### Web 開発者チーム

* Web アプリケーションの最新バージョンをデプロイし、`node.js` を再起動する。

言い換えると、Web 運用チームは、アプリケーションのデプロイメント用にサーバーを準備し、Web 開発者チームがそのサーバー上にアプリケーションをデプロイします。

---

作業を少し簡単にするために、必要なものはすべて Github リポジトリーに既に用意されています（Playbook、JSP ファイルなど）。ここではこれらを繋げるだけです。

> **注意**
>
> この例では、別々のチームのコンテンツに同じレポジトリーの異なる 2 つのブランチを使用します。実際には、Source Contorol レポジトリーの構造は状況にによって様々に異なります。

### プロジェクトのセットアップ

まず、通常どおりに Git リポジトリーをプロジェクトとして設定する必要があります。

> **警告**
>
> ユーザー **wweb** としてログインしている場合は、**admin** としてログインします。

**Automation Execution** -> **プロジェクト** 内で、**プロジェクトの作成** ボタンをクリックして、Web オペレーションチームのプロジェクトを作成します。以下のようにフォームに移動します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>名前</td>
    <td>Webops Git Repo</td>
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
  <tr>
    <td>ソースコントロールの URL</td>
    <td><code>https://github.com/ansible/workshop-examples.git</code></td>
  </tr>
  <tr>
    <td>Source control branch/tag/commit </td>
    <td><code>webops</code></td>
  </tr>
  <tr>
    <td>オプション</td>
    <td><ul><li>✓ Clean</li><li>✓ 削除</li><li>✓ Update revision on launch</li></ul></td>
  </tr>
</table>

**プロジェクトの作成** をクリックします。

---
**Automation Execution** -> **プロジェクト** 内で、**プロジェクトの作成** ボタンをクリックして、Web 開発者チームのプロジェクトを作成します。以下のようにフォームに移動します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>名前</td>
    <td>Webdev Git Repo</td>
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
  <tr>
    <td>ソースコントロールの URL</td>
    <td><code>https://github.com/ansible/workshop-examples.git</code></td>
  </tr>
  <tr>
    <td>Source control branch/tag/commit </td>
    <td><code>webdev</code></td>
  </tr>
  <tr>
    <td>オプション</td>
    <td><ul><li>✓ Clean</li><li>✓ 削除</li><li>✓ Update revision on launch</li></ul></td>
  </tr>
</table>

**プロジェクトの作成** をクリックします。

### ジョブテンプレートのセットアップ

次に、通常のジョブの場合と同じように、2 つのジョブテンプレートを作成する必要があります。

**Automation Execution** -> **テンプレート** に移動し、**テンプレートの作成** ボタンをクリックして、**ジョブテンプレートの作成** を選択します。

  <table>
    <tr>
      <th>パラメーター</th>
      <th>値</th>
    </tr>
    <tr>
      <td>名前</td>
      <td>Web App Deploy</td>
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
      <td>Webops Git Repo</td>
    </tr>
    <tr>
      <td>Playbook</td>
      <td><code>rhel/webops/web_infrastructure.yml</code></td>
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
      <td>✓ 権限昇格</td>
    </tr>
  </table>

**ジョブテンプレートの作成** をクリックします。

---

**Automation Execution** -> **テンプレート** に移動し、**テンプレートの作成** ボタンをクリックして、**ジョブテンプレートの作成** を選択します。

  <table>
    <tr>
      <th>パラメーター</th>
      <th>値</th>
    </tr>
    <tr>
      <td>名前</td>
      <td>Node.js Deploy</td>
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
      <td>Webdev Git Repo</td>
    </tr>
    <tr>
      <td>Playbook</td>
      <td><code>rhel/webdev/install_node_app.yml</code></td>
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
      <td>✓ 権限昇格</td>
    </tr>
  </table>

**ジョブテンプレートの作成** をクリックします。

> **ヒント**
>
> Ansible Playbook がどのようなものかを見たい場合は、Github URL を確認して適切なブランチに切り替えてください。

### ワークフローのセットアップ

ワークフローは **テンプレート** ビューで設定されます。テンプレートを追加するときに、**ジョブテンプレートの作成** と **ワークフロージョブテンプレートの作成** のどちらかを選択できることに気付いたかもしれません。

**リソース** -> **テンプレート** に移動し、**テンプレートの作成** ボタンをクリックして、**ワークフロージョブテンプレートの作成** を選択します。

  <table>
    <tr>
      <td><b>名前</b></td>
      <td>Deploy Webapp Server</td>
    </tr>
    <tr>
      <td><b>組織</b></td>
      <td>Default</td>
    </tr>
  </table>

**ワークフロージョブテンプレートの作成** をクリックします。

テンプレートを保存して、 **Add step** をクリックすると ワークフローを作成できます。テンプレートの詳細ページのボタンを使用して、メニューから **View workflow visualizer** を選択して、再度開くことができます。

  ![start](images/start.png)

**Add step** ウィンドウで、ノードタイプに **Job Template** を選択し、ノードにアクションを割り当てます。

**Web App Deploy** ジョブテンプレートを選択し、**Next** → **Finish** をクリックします。

  ![ノードの追加](images/add_node.png)

新しいノードが表示され、ジョブテンプレートの名前で **Start** ノードに接続されます。

  ![workflow node](images/workflow_node.png)

次に Web App Deployノードの **⋮** をクリックして **Add step and link** をクリックします。

**ノードタイプ** については、**Job Template** (デフォルト) を選択し、**Node.js Deploy** ジョブテンプレートを選択します。
**ステータス** を **Run on success** に設定して **Next** → **Finish** をクリックします。

  ![ノードの追加js](images/add_node_nodejs.png)

** Visualizer** ビューの **Save** をクリックして、編集したフローを保存します。

> **ヒント**
>
**ビジュアライザー** には、より高度なワークフローを設定するためのオプションがありますので、ドキュメントをご参照ください。

### ワークフローの起動

**Deploy Webapp Server** 詳細 ページから、テンプレートを起動します。

  ![起動](images/launch.png)

ジョブ > Deploy Webapp Server 出力 にワークフローの実行が表示されています。通常のジョブテンプレートのジョブ実行とは異なり、ジョブが完了しても Playbook の出力はありませんが、ジョブが完了するまでの時間が表示されています。実際の Playbook の実行内容を見たい場合は、詳細を確認したいノードにカーソルを合わせてクリックします。ジョブの詳細表示の中で、**出力** メニューを選択すると、Playbook の出力が表示されます。 **Deploy WebappServer** ワークフローの **出力** ビューに戻りたい場合は、Automation Execution -> ジョブ -> **Deploy Webapp Server** で 出力 の概要に戻ることができます。 

![jobs view of workflow](images/job_workflow.png)

ジョブが完了したら、すべてが正常に動作したかどうかを確認します。コントロールホストから、`node1`、`node2`、および `node3` に対して以下の curl コマンドを実行します。各 curl コマンドの出力は `Hello World` のはずです。

```bash
[student@ansible-1 ansible-files]$ curl http://nodeX/nodejs
Hello World
```

注記: `X` は、チェックするノードの適切な番号に置き換える必要があります。


---
**ナビゲーション**
<br>
[前の演習](../2.5-rbac/README.ja.md) - [次の演習](../2.7-wrap/README.ja.md)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-tower-exercises)
