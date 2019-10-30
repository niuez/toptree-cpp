# Cluster

Linkも書いたしそろそろクエリを捌けるようにしたい, `struct Cluster`を定義します. 

```cpp
struct cluster {

  using V = int;
  cluster() {}
  static cluster identity() {
    return cluster();
  }
  static cluster compress(const cluster& a, const cluster& b, V av, V bv, V cv) {
    //
  }
  static cluster rake(const cluster& a, const cluster& b, V av, V bv, V cv) {
    //
  }
  static cluster reverse(const cluster& c) {
    //
  }
};
```


