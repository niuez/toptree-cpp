# Node

toptreeに載せるノードを表す`class node`を書きます

```cpp
enum class Type { Compress, Rake, Edge, None };

class node {
  node* ch[2];
  node* par;
  node* ra;
  node* me;
  bool rev;
  vertex v[2];
  Type ty;


public:

  node(): par(nullptr), ra(nullptr), me(nullptr), rev(false), ty(Type::None) {} 

  static node* new_edge(vertex v, vertex u, cluster val) {
    node* n = new node();
    n->v[0] = v;
    n->v[1] = u;
    n->me = n;
    n->ty = Type::Edge;
    n->fix();
    return n;
  }

  static node* new_compress(node* left, node* right) {
    node* n = new node();
    n->ch[0] = left;
    n->ch[1] = right;
    n->me = n;
    n->ty = Type::Compress;
    n->fix();
    return n;
  }

  static node* new_rake(node* left, node* right) {
    node * n = new node();
    n->ch[0] = left;
    n->ch[1] = right;
    n->me = n;
    n->ty = Type::Rake;
    n->fix();
    return n;
  }

  inline void fix() {
    if(this->ty == Type::Edge) {
      if(!this->parent()) {
        this->endpoint(0).set_handle(this->me);
        this->endpoint(1).set_handle(this->me);
      }
      else if(this->parent()->ty == Type::Compress) {
        if(parent_dir(this->me) == -1) {
          this->endpoint(0).set_handle(this->me);
        }
      }
      else if(this->parent()->ty == Type::Rake) {
        this->endpoint(0).set_handle(this->me);
      }
    }
    else if(this->ty == Type::Compress) {
      this->push();
      this->v[0] = this->child(0)->endpoint(0);
      this->v[1] = this->child(1)->endpoint(1);
      assert(this->child(0)->endpoint(1) == this->child(1)->endpoint(0));

      this->child(0)->endpoint(1).set_handle(this->me);

      if(!this->parent()) {
        this->endpoint(0).set_handle(this->me);
        this->endpoint(1).set_handle(this->me);
      }
      else if(this->parent()->ty == Type::Compress) {
        if(parent_dir(this->me) == -1) {
          this->endpoint(0).set_handle(this->me);
        }
      }
      else if(this->parent()->ty == Type::Rake) {
        this->endpoint(0).set_handle(this->me);
      }

    }
    else if(this->ty == Type::Rake) {
      this->push();
      this->v[0] = this->child(0)->endpoint(0);
      this->v[1] = this->child(0)->endpoint(1);
    }
    else { assert(false); }
  }

  inline void push() {
    if(this->ty == Type::Compress) {
      if(this->rev) {
        std::swap(this->ch[0], this->ch[1]);
        this->child(0)->reverse();
        this->child(1)->reverse();
        this->rev = false;
      }
    }
  }

  inline void reverse() {
    if(this->ty == Type::Edge) {
      std::swap(this->v[0], this->v[1]);
    }
    else if(this->ty == Type::Compress) {
      std::swap(this->v[0], this->v[1]);
      this->rev ^= true;
    }
    else if(this->ty == Type::Rake) {
    }
    else { assert(false); }
  }

  inline node* parent() const { return this->par; }
  inline void set_parent(node* par) { this->par = par; }
  inline node* rake() const { return this->ra; }
  inline void set_rake(node* rake) { this->ra = rake; }
  inline node* child(std::size_t dir) const { return this->ch[dir]; }
  inline void set_child(node* ch, std::size_t dir) { this->ch[dir] = ch; }
  inline vertex endpoint(std::size_t dir) { return this->v[dir]; }
  inline Type type() const { return this->ty; }
};
```
