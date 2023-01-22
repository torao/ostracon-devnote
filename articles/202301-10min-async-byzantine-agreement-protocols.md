---
title: "10分で論文略読: Asynchronous Byzantine Agreement Protocols (1987)"
emoji: "🛋️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["分散システム","BFT"]
published: true
---

# Asynchronous Byzantine Agreement Protocols

Lamport, Shostak, Pease の [The Byzantine General Problem](20230108-10min-the-byzantine-generals-problem) (1983) ではシステムが許容可能な故障プロセス数を $t$ としたときに全プロセス数が $n \le 3t$ ではビザンチン合意を達成することができないという下限が証明されている。この論文はその下限の $n=3f+1$ でビザンチン合意を達成できる(おそらく)最初の非同期プロトコルを提示し、概ねその証明や背景説明で構成されている。その核心のプロトコルは Fig.4 に示されていて Bracha Broadcast と呼ばれる。最初の double-echo アルゴリズム (single shot 版) であり、次の現実的なシステムで見られる。
- Introduction to reliable and secure distributed programming (2011)
- Secure intrusion-tolerant replication on the internet (2002)
- Asynchronous BFT Made Practical (2018)

> BRACHA, Gabriel. Asynchronous Byzantine agreement protocols. _Information and Computation_, 1987, 75.2: 130-143.

## Abstract

コンセンサスプロトコルは $n$ 個の非同期プロセスで構成されるシステムで、そのうちのいくつかは故障しているが合意に達することを可能にしている。故障プロセスもメッセージシステムも正しいプロセスが決定に達するのを妨害するために協力することができる。最大 $t$ 個の故障プロセスが存在する場合、確率 1 で合意に達するプロトコルは $t$-resilient と呼ばれる。ビザンチンプロセスとは、プロトコルから任意に逸脱できる故障プロセスであり、フェイルストッププロセスはプロトコルへの参加を停止るだけである。最近の論文では $t \lt n/5$ に対して $t$-resilient なランダム化コンセンサスプロトコルが発表された。我々はこれを $t \lt n/3$ に改善し、コンセンサスに必要な正常プロセス数の既知の下限と一致させる。このプロトコルは、メッセージの一部をフィルタリングするブロードキャストプロトコルの使用によってビザンチンプロセスの振る舞いを制限する一般的な技法を用いている。ブロードキャストプロトコルによってフィルタリングされたビザンチンプロセスの見掛け上の振る舞いは、フェイルストップのプロセスの振る舞いに似ている。ブロードキャストプロトコルを通信プリミティブとして、フェイルストッププロセスのための合意プロトコルに差し込むと、このような結果になる。ブロードキャストプロトコルを使って故障プロセスの力を弱め、より弱い故障モデルに対して設計されたアルゴリズムの通信プリミティブとして使うこの手法は、他の文脈でも成功裏に使用されている。

## 1. Introduction

- Agreement: すべての正常なプロセスは同じ値を決定する
- Validity: すべての正常なプロセスが同じ値 $v$ で開始すると、すべての正常なプロセスは値 $v$ で決定する。

全 $n$ 個のプロセスのうち $t$ 個の故障プロセスを想定。まずメッセージの紛失や捏造がなく、送信者を特定できる完全グラフネットワークを想定する。同期システムでのプロトコルの動きは各プロセスの初期値と故障プロセスの動作で決まる。非同期システムではそれにスケジューラーを導入する。スケジューラーは敵対的で、各プロセスの $n$ 個のメッセージのうちどの $n-t$ 個を受信させるかをラウンドごとに決めることができる。

決定論的 Termination は非同期環境では FLP Impossibility により達成できない。*ランダム化されたコンセンサス*は FLP Impossibility を回避するためにランダムさを加えて確率的 Termination 特性を持つ。

## 2. Reliable Broadcast

あるプロセス $p$ が他のすべてのプロセスに自信の値をブロードキャストする。これは次の条件が満たされれば「高信頼性ブロードキャスト」である。

1. $p$ が正常なとき、すべての正常なプロセスなメッセージの値に合意する。
2. $p$ が故障のとき、すべての正常なプロセスは同じ値に合意するか、または $p$ からのいかなる値も受理しない。

### 2.1 The Broadcast Primitive

この論文で紹介するプロトコルのメッセージは initial, echo, ready の 3 種類。

- (initial, $v$) は $p$ が値 $v$ を送信したいことを意味する。
- (echo, $v$) は送信者が $p$ から (initial, $v$) を受信しているか、またはそれを裏付ける十分な (echo, $v$) または (ready, $v$) を受信して $p$ が $v$ を送信したことを知っていることを意味する。 
- (ready, $v$) は $v$ が $p$ によって送信された唯一の値であり、また十分な (echo, $v$) または (ready, $v$) を受信しているため $v$ を受理する準備があることを意味する。

あるプロセスが十分な (ready, $v$) を受信すると、他のすべての正しいプロセスが $v$ を受理するに違いないと知るので、$p$ が送信した値として $v$ を受理する。

```
* Protocol 1 *
Broadcast(v)
step 0. (By process p)
  Send (initial, v) to all the processes.

step 1. Wait until the receipt of,
    one (initial, v) message
    or (n+t)/2 (echo, v) messages
    or (t+1) (ready, v) messages
    for some v.
  Send (echo, v) to all the processes.

step 2. Wait until the receipt of,
	(n+t)/2 (echo, v) messages
	or t+1 (ready, v) messags
	(including messages received in step 1)
	for some v.
  Send (ready, v) to all the processes.

step 3. Wait until the receipt of,
	2t+1 (ready, v) messages
	(including messages received in step 1 and step 2) for some v.
  Accept v.
```
Fig. 1. The broadcast primitive

### 2.2 Correctness Proof

> **Lemma 1**. 2 つの正常なプロセス $s$ と $t$ がそれぞれ (ready, $v$) と (ready, $u$) メッセージを送信するとき、$v=u$ である。

証明. (ready, $v$) を送信するには $\frac{n+t}{2}$ より多いの (echo, $v$) が必要で、(ready, $u$) を送信するのにも $\frac{n+t}{2}$ より多い  (echo, $u$) が必要。しかし正常なプロセスは 1 度のブロードキャストで 1 つのメッセージのみ送ることができる。

> **Lemma 2**. 2 つの正常なプロセス $q$ と $r$ がそれぞれ $v$ と $u$ の値を受理したとき、$v=u$ である。

証明. $q$ が $v$ を受理するとき、$2t+1$ 個の (ready, $v$) を受信していなければならない。$r$ も同様に $2t+1$ 個の (ready, $u$) を受信していなければならない。両者とも少なくとも $t+1$ 個の正常なプロセスからのメッセージであるため $v=u$ である。

> **Lemma 3**. 正常なプロセス $q$ が値 $v$ を受理するとき、他のすべてのプロセスは最終的に $v$ を受理する。

証明. $q$ が $v$ を受理するには $2t+1$ 個の (ready, $v$) が必要で、少なくともその中の $t+1$ 個は正常なプロセスから送信されたものである。したがってほかの正常なプロセスも $t+1$ 個の (ready, $v$) を受信しており、自身の (ready, $v$) も送信している。Lemma 1 により正常なプロセスは異なる ready メッセージを送らない。したがって少なくとも $n-t$ 個のプロセスが (ready, $v$) メッセージを送信している。すべての正常なプロセスは最終的に少なくとも $2t+1 \le n-t$ 個の (ready, $v$) メッセージを受信し、$v$ を受理する。

> **Lemma 4**. 正常なプロセス $p$ が値 $v$ をブロードキャストするとき、すべての正常なプロセスは $v$ を受理する。

証明. $p$ が (initial, $v$) をブロードキャストすると、すべての正常なプロセス $q$ は (echo, $v$) で応答し、$q$ は $n-t \gt \frac{n+t}{2}$ 個の (echo, $v$) を受信し、$q$ は (ready, $v$) を送信する。ステップ 3 ですべての正常なプロセスは $n-t \ge 2t+1$ 個の (ready, $v$) を受信しているので、$q$ は $v$ を受理する。

> **定理 1**. Fig 1 のプロトコルは高信頼性ブロードキャストプロトコルである。

証明. $p$ が値 $v$ をブロードキャストしたとき:

1. $p$ が正常であれば、Lemma 4 よりすべての正常なプロセスは $v$ を受理する。
2. $p$ が故障であり、ある正常なプロセス $q$ が値 $v$ を受理すれば、Lemma 3 よりすべての正常なプロセスは $v$ を受理する。さもなくばすべての正常なプロセスは値を受理しない。

## 3. Correctness Enforcement

$p$ がラウンド $k$ で送ったメッセージを $(p,k,v)$ とする。各ラウンド $k$ ですべてのプロセス $p$ は ${\sf VALID}_p^k$ の集合を扱う。ここで ${\sf VALID}_p^1$ は各 $q$ に対する $(q,1,v)$ の集合である。

> **Lemma 5**. 2 つの正常なプロセス $p$ と $q$ がそれぞれ $(r,k,v)$ と $(r,k,u)$ メッセージを検証したとき、$u=v$ である。

証明. $p$ が $v$ を検証  (validate) したとき、それを受理しなければならない。$q$ も同様。定理 1 より $v=u$ である。

> **Lemma 6**. 正常なプロセス $p$ が $k$-メッセージ $m$ を検証するとき、他のすべての正常なぷp炉セス $q$ は $m$ を検証する。つまり $p$ と $q$ が正常であれば ${\sf VALID}_p^k = {\sf VALID}_q^k$ である。

証明. $k=1$ のとき、定理 1 によりなされる。─

> **Lemma 7**. 正常なプロセス $p$ が $k$-メッセージ $m$ をブロードキャストするとき、すべての正常なプロセスは最終的に $m$ を検証する。

```
round(k) by process p
  Send (p, k, v) to all the processes
  Wait until a set S of n-t messages from round k have been received
  v := N (k, S)
```
Fig. 2. A round of general synchronous protocol.

```
round(k) by process p
  Broadcast (p, k, v)
  Wait till a set S of n-t k-messages have been validated
  v := N (k, S)
```
Fig. 3. A modified round of a general asynchronous protocol.

## 4. The Consensus Protocol

$n/3$-resilient プロトコルを説明する。Ben-Or (1983) と Bracha (1983), Touge (1985) のプロトコル。

- ラウンド: 1 回のブロードキャスト。
- フェーズ: 1 回の合意シーケンス。1 フェーズは 3 ラウンドで構成される。
- $k$-メッセージ: ラウンド $k$ に送信されるメッセージ。

## 5. Correctness Proof

Fig. 4 は $t$-resilient なコンセンサスプロトコルである。$t \lt n/3$ に対して、という証明を行う。待ちが入るのでデッドロックしないことを示す。

> **Lemma 8**. 正常なプロセス $p$ がラウンド $i$ にいるとき、$p$ は最終的にラウンド $i+1$ へ進行する。

証明. もしそうでないと、ある正常なプロ祖エスは永遠にブロックする。プロセス $p$ が永遠にブロックを開始したラウンドを $r$ とすると、すべての正常なプロセスはこのラウンドでメッセージを送信しているはずであるある。Lemma 7 より、すべてのメッセージは最終的に県しょすあれている。したがって $p$ はラウンド $r$ でブロックされていない。

> **Lemma 9**. ラウンド $3r+1$ の開始ですべての正しいプロセスが値 $v$ を持つとき、それらはすべてラウンド $3r+3$ で $v$ を決定する。

証明. すべての $n-t$ 個の正常なプロセスはラウンド $3r+1$ で値 $v$ を持っている。したがってすべての正しいプロセスはラウンド $3r+1$ で $v$ の少なくとも $n-2t$ メッセージを検証する。$t \lt n/3$ に対して $n-2t \gt \frac{n-t}{2}$ より、すべての正しいプロセスはラウンド $3r+1$ でその値を保持する。Lemma 8 より、すべての正常なプロセスはラウンド $3r+2$ と $3r+3$ に進行する。ラウンド $3r+2$ で $u\ne v$ となるようなメッセージを検証するためには正常なプロセスは少なくとも $\frac{n-t}{2} \gt t$ 個のメッセージが、ラウンド $3r+1$ からの値 $u$ で検証しなければならない。これは明らかに不可能である。したがって、可能な値はラウンド $3r+2$ で $v$ である。そしてすべての正常なプロセスは $(d,v)$ へ値を変更する。ラウンド $3r+2$ では、すべての正常なプロセスは少なくとも $(d,v)$ の $2t+1$ メッセージを検証し $v$ を決定する。

> **Lemma 10**. $p$ と $q$ を正常なプロセスとする。フェーズ $r$ で $p$ が $(d,v)$ のメッセージを検証し、$q$ が $(d,u)$ のメッセージを検証するとき、$u=v$ である。

証明. ─

>**定理 2**. Fig. 4 で述べたプロトコルは、$t \lt n/3$ に対して $t$-relisient なコンセンサスプロトコルである。

証明. 
- Validity: Lemma 9 より、値 $v$ で開始したプロセスはすべて $v$ で終了する。
- Agreement: ラウンド $3r+3$ で $p$ が 1 に決めたとき、少なくとも $2t+1$ 個の $(d,1)$ メッセージを検証しているはずであり、Lemma 6 より他のすべての正常なプロセスは少な渥とも $t+1$ 個の同じメッセージを検証している。したがってラウンド $3r+3$ のステップ (ii) ですべての正常なプロセスは $v_q=1$ を決定している。フェーズ $r+1$ の開始ですべての正常なプロ背えすは 1 を持ち、Lemma 9 によりフェーズ $r+1$ の終わりに 1 に決定する。
- Termination: フェーズ $r$ について、いくつかのプロセスはステップ (ii) でラウンド $3r+3$ の値を強制され、残りのプロセスは (iii) で恋んトスで値を決定するとする。このとき 2 ケースが考えられる。
	1. $p$ は $(d,v)$ を検証した。フェーズ $r$ でコイントスしたすべての正常なプロセスは $p \gt 2^{-(n-t)}$ の確率で $v$ をトスする。Lemma 10 より、正常なプロセスの残りは値を $v$ に決定する。
	2. $p$ は $(d,v)$ を検証していない。─

いずれも $2^{-(n-t)}$ より大きい確率で、すべてのプロセスはフェーズ $r+1$ の開始時に同じ値を持ち、Lemma 9 により $r+1$ で決定される。

Bracha and Toueg (1985) では $t \ge n/3$ では $t$-resilient は不可能であると証明した。したがってこのプロトコルは許容可能な故障プロセス数に対して最適である。

```
* Protocol 2 *
Phase(i): (by process p)
1.  Broadcast (p, 3i+1, value[p]). Wait until validate n-t 3i+1-messages.
	value[p] := majoriby value of the n-t validated messages.
2.  Broadcast (p, 3i+2, value[p]). Wait until validate n-t 3i+2-messages.
	(i) If more than n/2 of the messages have the same value v, then value[p] := v
	(ii) Otherwise, value[p] := value[p].
3.  Broadcast (p, 3i+3, value[p]). Wait until validate n-t 3i+3-messages.
	(i) If validated more than 2t messages with value (d, v) then decition[p] := value[p] := v
	(ii) If validated more than t messages with value (d, v) then value[p] = v.
	(iii) Otherwise, value[p] := coin_toss(0 or 1 with probability 1/2).
	Go to round 1 of phase i+1
```
Fig. 4. The consensus protocol.

## 6. Performance

> **定理 3**. (i) 定数 $c$ に対して $t = c \cdot n$ のとき、合意に達する期待フェーズ数は $n$ のべき乗である。 (ii) 定数 $c$ に対して $t=c \sqrt{n}$ のとき、合意に達する期待フェーズ数は $n$ に依存しない (ただし $c$ に対して指数的である)。

証明. Ben-Or (1983) にある。

定理 3  は非同期のケース。同期の場合は注意が必要。

## 7. Rabin's Model

期待ラウンド 4 の $\frac{n}{10}$-resilient プロトコル。信頼できるディーラーがコイントス列を計算し、すべてのプロセスに配布する想定 (trusted setup?)。秘密分散を使用する。

## 8. Asynchronous Byzantine General Protocol

非同期ではビザンチン将軍プロトコルは termination 特性を達成できない。

> **定理 4**. ビザンチン将軍プロトコルは、ただ一つの故障プロセスの存在で非同期環境では不可能である。

証明. 非同期環境でビザンチン将軍プロトコルが可能だと仮定して次のシナリオを考える。

1. 送信者が故障しているとき、プロトコル中にほかの正常なプロセスにメッセージを送信しない。Termination 特性によりプロセスは同じ値、我々の場合は 0 に、$T_0$ で合意する。
2. 送信者と他のプロセスが正常で、プロトコルに従って $1$-メッセージを送るが、時刻 $T_0$ までにメッセージが到着しなかった。正常なプロセスはシナリオ 1 のように尾奈ｊビューを持ち、従って $T_0$ で 0 に決定する。これは Validity 特性に違反する。

**Weak Termination**: 送信者が正常なら、すべての正常なプロセスは最終的に値を決定する。送信者が故障していれば、すべての正常なプロセスは最終的に決定するか、誰も決定しないかのいずれかである。

> **定理 5**. 非同期システムにおいて、ビザンチン将軍プロトコルは Weak Termination で $t \lt n/3$ でのみ可能である。

証明. $t \lt n/3$ であれば Fig. 1 は Weak Termination のビザンチン将軍プロトコルである。$t \ge n/3$ での $t$-resilient なビザンチン将軍プロトコルが存在すると仮定する。A, B, C の 3 つの離散的な集合にプロセスが分けられているとする。A, B, C の大きさは $t$ 以下である。送信者が A に属し、次のシナリオを考える。

1. A と B のプロセスが正常で、送信者は $0$-メッセージを送る。C のプロセスは故障で、プロトコル中にメッセージを送信しない。送信者が正常で、最大でも $t$ 個のプロセスが故障しているため、A と B のプロセスは同時刻 $T_0$ に 0 に合意しなければならない。
2. 送信者のみが故障しているとき、A と B のプロセスには $0$-メッセージを、C のプロセスには $1$-メッセージを送る。C からのメッセージは遅延し $T_0$ より後に受信される。A と B はシナリオ 1 と同じシステムのビューを持ち、したがって時刻 $T_0$ に 0 で合意する。

同様の方法で 3 つ目のシナリオを構築できる。

3. 送信者のみが故障ｙしている場合、$1$-メッセージを A と B に送信し、$0$-メッセージを C に送信する。B からのメッセージは遅延し、$T_0$ には受信されない。$T_1$ で A と C のプロセスは 1 に合意する。

シナリオ 2 と 3 を組み合わせるとシナリオ 4 となり矛盾が生じる。

4. A のプロセスが故障しており、B と C のプロセスは正常である。A のプロセスはシナリオ 2 のように B のプロセスに、またシナリオ 3 のように C のプロセスに送信すると、B のプロセスと C のプロセスの間のすべてのプロセスは遅延し、$\max(T_0,T_1)$ まで受信されない。このシナリオでは $\max(T_0,T_1)$ に B のプロセスが 0 に合意し、C のプロセスは 1 に合意するため矛盾する。