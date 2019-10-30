# Link

link操作を書きます

```cpp
node* link(vertex v, vertex u) {
  if(!v.handle() && !u.handle()) {
    return node::new_edge(v, u);
  }
  else {
    node* nnu = u.handle();
    node* nnv = v.handle();
    node* e = node::new_edge(v, u);
    node* left = nullptr;

    if(!nnu) { left = e; }
    else {
      node* uu = expose_raw(nnu);
      uu->push();
      if(uu->endpoint(1) == u) {
        uu->reverse();
        uu->push();
      }
      if(uu->endpoint(0) == u) {
        node* nu = node::new_compress(e, uu);
        e->set_parent(nu);
        e->fix();
        uu->set_parent(nu);
        uu->fix();
        nu->fix();

        left = nu;
      }
      else {
        node* nu = uu;
        node* left_ch = nu->child(0);
        left_ch->push();

        nu->set_child(e, 0);
        e->set_parent(nu);
        e->fix();
        
        node* beta = nu->rake();
        node* rake = nullptr;
        if(beta) {
          beta->push();
          rake = node::new_rake(beta, left_ch);
          beta->set_parent(rake);
          left_ch->set_parent(rake);
          beta->fix();
          left_ch->fix();
        }
        else {
          rake = left_ch;
        }
        nu->set_rake(rake);
        rake->set_parent(nu);
        rake->fix();
        nu->fix();

        left = nu;
      }
    }

    if(!nnv) {}
    else {
      node* vv =expose_raw(nnv);
      vv->push();
      if(vv->endpoint(0) == v) {
        vv->reverse();
        vv->push();
      }
      if(vv->endpoint(1) == v) {
        node* top = node::new_compress(vv, left);
        vv->set_parent(top);
        left->set_parent(top);
        vv->fix();
        left->fix();
        top->fix();
      }
      else {
        node* nv = vv;
        node* right_ch = nv->child(1);
        right_ch->reverse();
        right_ch->push();

        nv->set_child(left, 1);
        left->set_parent(nv);
        left->fix();

        node* alpha = nv->rake();
        node* rake = nullptr;
        if(alpha) {
          alpha->push();
          rake = node::new_rake(alpha, right_ch);
          alpha->set_parent(rake);
          alpha->fix();
          right_ch->set_parent(rake);
          right_ch->fix();
        }
        else {
          rake = right_ch;
        }
        nv->set_rake(rake);
        rake->set_parent(nv);
        rake->fix();
        nv->fix();
      }
    }

    return e;
  }
}
```
