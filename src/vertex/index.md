# Vertex

まず, 木の頂点を表現する`class vertex`を書くことにします.

```cpp
class vertex_raw {
  int val;
  node* hand;

public:

  vertex_raw(int val): val(val), hand(nullptr) {}

  node* handle() const { return this->hand; }
  void set_handle(node* hand) { this->hand = hand; }
  int value() const { return this->val; }
};

class vertex {
  vertex_raw* ver;

public:

  static vertex dangling() { return vertex(); } 

  vertex(): ver(nullptr) {}
  vertex(cluster::V val): ver( new vertex_raw(val)) {
    vertex dummy;
    dummy.ver = new vertex_raw(-1);
    link(*this, dummy);
  }

  bool operator==(const vertex& other) { return this->ver == other.ver; }
  
  inline node* handle() const { return this->ver->handle(); }
  inline void set_handle(node* hand) { this->ver->set_handle(hand); }
  inline int value() const { return this->ver->value(); }
};
```
