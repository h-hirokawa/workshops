# ワークショップ演習 - ロールベースのアクセス制御

**他の言語でもお読みいただけます**:
<br>![uk](../../../images/uk.png) [English](README.md)、![japan](../../../images/japan.png)[日本語](README.ja.md)、![brazil](../../../images/brazil.png) [Portugues do Brasil](README.pt-br.md)、![france](../../../images/fr.png) [Française](README.fr.md)、![Español](../../../images/col.png) [Español](README.es.md)

## 目次

* [目的](#目的)
* [ガイド](#ガイド)
* [Ansible Automation Controller ユーザー](#ansible-Automation Controller ユーザー)
* [Ansible Automation Controller チーム](#ansible-Automation Controller チーム)
* [パーミッションの付与](#パーミッションの付与)
* [パーミッションのテスト](#パーミッションのテスト)

## 目的

Ansible Automation Controller が認証情報をユーザーから分離する方法をすでに学習しました。Ansible Automation Controller のもう 1 つの利点は、ユーザーとグループの権限管理です。この演習では、役割ベースのアクセス制御 (RBAC) について説明します。

## ガイド

### Ansible Automation Controller ユーザー

Automation Controller ユーザーには、以下の 3 つのタイプがあります。

* **Normal User (通常ユーザー)**: このユーザーには、適切なロールや権限が付与されているユーザーのインベントリーやプロジェクトに限定される読み取りおよび書き込みアクセスがあります。

* **System Auditor (システム監査者)**: この監査者は、Automation Controller 環境内のすべてのオブジェクトの読み取り専用機能を暗黙的に継承します。

* **System Administrator (システム管理者)**: このユーザーには、Automation Controller インストール全体にわたる管理者、読み込み、書き込み特権があります。

ユーザーを作成しましょう。

* **アクセス** 下のメニューで、**ユーザー** をクリックします。

* **追加** ボタンをクリックします。

* 新しいユーザーの値を入力します。

<table>
  <tr>
    <th>パラメーター</th>
    <th>値</th>
  </tr>
  <tr>
    <td>姓</td>
    <td>Web</td>
  </tr>
  <tr>
    <td>名</td>
    <td>Werner</td>
  </tr>
  <tr>
    <td>メール</td>
    <td>wweb@example.com</td>
  </tr>
  <tr>
    <td>ユーザー名</td>
    <td>wweb</td>
  </tr>
  <tr>
    <td>パスワード</td>
    <td>ansible</td>
  </tr>
  <tr>
    <td>パスワードの確認</td>
    <td>ansible</td>
  </tr>
  <tr>
    <td>ユーザータイプ</td>
    <td>標準ユーザー</td>
  </tr>
  <tr>
    <td>組織</td>
    <td>Default</td>
  </tr>
</table>

* **保存** をクリックします。

### Ansible Automation Controller チーム

Team (チーム) は、関連付けられたユーザー、プロジェクト、認証情報、およびパーミッションを持つ組織の下位区分のことです。チームは、ロールベースのアクセス制御スキームを実装し、組織全体で責任を委任する手段となります。たとえば、パーミッションは、チームの各ユーザーではなく、チーム全体に付与することができます。

チームの作成:

* メニューで **アクセス → チーム** に移動します。

* **追加** ボタンをクリックして、`Default` 組織内に `Web Content` という名前のチームを作成します。

* 保存 をクリックします。

ユーザーをチームに追加できるようになりました。

* チーム `Web Content` をクリックし、**アクセス タブをクリックして **追加** をクリックします。

* **リソースタイプの選択** ウィンドウで、**ユーザー** リソースタイプをクリックし、**次へ** をクリックします。

* **リストからアイテムの選択** で、`wweb` ユーザーの横にあるチェックボックスを選択し、**次へ** をクリックします。

* **適用するロールの選択** で、`wweb` ユーザーに適用するロールとして **メンバー** を選択します。

**保存** をクリックします。

パーミッションにより、プロジェクト、インベントリ、およびその他のAutomation Controller 要素の読み取り、変更、および管理が可能になります。さまざまなリソースにパーミッションを設定できます。

### パーミッションの付与

ユーザーまたはチームが実際に何かを実行できるようにするには、パーミッションを設定する必要があります。ユーザー **wweb** は、割り当てられた Web サーバーのコンテンツのみを変更できるようにする必要があります。

`Create index.html` テンプレートを使用するためのパーミッションを追加します。

* **リソース** -> **テンプレート**内で、`Create index.html` を選択します。

* メニューから **アクセス** タブを選択し、**追加** をクリックします。

* **リソースタイプの選択** ウィンドウで、**ユーザー** リソースタイプをクリックし、**次へ** をクリックします。

* **リストからアイテムの選択** で、`wweb` ユーザーの横にあるチェックボックスを選択し、**次へ** をクリックします。

* **適用するロールの選択** で、`wweb` ユーザーに適用するロールとして**Read** と **Execute** を選択します。

* **保存** をクリックします。

### パーミッションのテスト

次に、Automation Controller の Web UI からログアウトし、**wweb** ユーザーとして再度ログインします。

* **テンプレート**ビューに移動します。wweb については、`Create index.html` テンプレートのみが表示されます。このテンプレートを表示および起動することはできますが、編集することはできません。(編集ボタンは利用できません)

* ロケットアイコンをクリックして、Job Template を実行します。好みに合わせて survey 内容を入力し、ジョブを開始します。

* 次の **ジョブ** ビューをよくみてみましょう。どのタスクが変更されているかに注意してください。

結果の確認: コントロールホストで `curl` を再び実行し、`node1` の Web サーバーのコンテンツを取得します (`node2` や
`node3` も同様)。

```bash
#> curl http://node1
```

今行ったことを思い出してください。これで、ユーザーが制限された範囲で AnsiblePlaybook を実行できるようにしました

* 認証情報にアクセスせずに

* Playbook 自体を変更せずに

* 事前に定義した変数のみを変更できる状態で

この機能によって、認証情報を配布したり、ユーザーに自動化コードを変更する機能を与えたりすることなく、別のユーザーに自動化を実行させる機能を提供できます。また、同時に、ユーザーは作成した survey に基づいて変更を加えることもできます。

この機能は、Ansible Automation Controller の主な強みの1つです。

---
**ナビゲーション**
<br>
[前の演習](../2.4-surveys/README.ja.md) - [次の演習](../2.6-workflows/README.ja.md)

[Click here to return to the Ansible for Red Hat Enterprise Linux Workshop](../README.md#section-2---ansible-tower-exercises)
