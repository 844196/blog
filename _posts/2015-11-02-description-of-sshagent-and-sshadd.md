---
layout: post
title: ssh-agentとssh-add
date: 2015-11-02 23:37
---

manの書き起こし

## ssh-agent

秘密鍵を管理する。

認証エージェントが起動している状態で`ssh`等を実行すると、`ssh`は`SSH_AUTH_SOCK`にあるUNIXソケットファイルを経由して認証エージェントとやり取りし、秘密鍵が登録されている場合は認証エージェントが必要な認証を代行する。

### <i class="fa fa-globe fa-fw"></i> 環境変数

- `SSH_AUTH_SOCK`
    - 認証エージェントが作成したUNIXソケットファイルへのパス
- `SSH_AGENT_PID`
    - 起動した認証エージェントのプロセスID

### <i class="fa fa-sun-o fa-fw"></i> 起動

<div class="mermaid">
graph LR

    subgraph 環境変数
        SSH_AGENT_PID
        SSH_AUTH_SOCK
    end

    subgraph $TMPDIR/ssh-XXX/agent.XXX
        UNIXソケットファイル
    end

    ssh-agent-- 作成 -->UNIXソケットファイル
    ssh-agent-- 定義 -->SSH_AGENT_PID
    ssh-agent-- 定義 -->SSH_AUTH_SOCK

    UNIXソケットファイル-- ソケットファイルのパス ---SSH_AUTH_SOCK

</div>

実行すると認証エージェントが起動する。

同時に、標準出力へ環境変数を設定と認証エージェントのプロセスIDを出力するコードを吐き出すので、`eval`することが前提。

```bash
$ ssh-agent
SSH_AUTH_SOCK=/tmp/ssh-RisMJzx3gFiV/agent.8320; export SSH_AUTH_SOCK;
SSH_AGENT_PID=8321; export SSH_AGENT_PID;
echo Agent pid 8321;
```

また、`$TMPDIR/ssh-<なんか適当な文字列>/agent.<プロセスID>`へ[^1]UNIXソケットファイルを作成する。

[^1]: `TMPDIR`が定義されていない場合は`/tmp`になるっぽい

### <i class="fa fa-bed fa-fw"></i> 終了

`-k`で`SSH_AUTH_SOCK`を削除し、プロセスID:`SSH_AGENT_PID`を終了させる。要は認証エージェントを終了させる。

同時に認証エージェントが定義した環境変数の定義を外すコードを吐き出すので、やっぱり`eval`が前提。

```bash
$ ssh-agent -k
unset SSH_AUTH_SOCK;
unset SSH_AGENT_PID;
echo Agent pid 8321 killed;
```

## ssh-add

<div class="mermaid">
graph LR

    subgraph 環境変数
        SSH_AUTH_SOCK
    end

    subgraph $TMPDIR/ssh-XXX/agent.XXX
        UNIXソケットファイル
    end

    UNIXソケットファイル-- ソケットファイルのパス ---SSH_AUTH_SOCK

    ssh-add-- 参照 -->SSH_AUTH_SOCK
    ssh-add-- 経由 -->UNIXソケットファイル
    UNIXソケットファイル-- 秘密鍵を登録 -->認証エージェント

</div>

認証エージェントへ秘密鍵を登録する。秘密鍵にパスフレーズが設定されている場合は入力を促される。

秘密鍵を引数で指定されなかった場合、以下のファイルが登録される。

- `~/.ssh/identity`
- `~/.ssh/id_dsa`
- `~/.ssh/id_ecdsa`
- `~/.ssh/id_ed25519`
- `~/.ssh/id_rsa`

認証エージェントとのやり取りは、`SSH_AUTH_SOCK`を経由して行われる。

### <i class="fa fa-globe fa-fw"></i> 環境変数

- `SSH_AUTH_SOCK`
    - 認証エージェントとやり取りするためのUNIXソケットファイルへのパス
    - 基本的に認証エージェントが起動時に定義する

## 参考

- <http://euske.github.io/openssh-jman/ssh-agent.html>
- <http://euske.github.io/openssh-jman/ssh-add.html>
