# Expose

splayを組み合わせて, expose操作を書きます

```cpp
node* expose_raw(node* t) {
  while(true) {
    assert(t->type() != Type::Rake);
    if(t->type() == Type::Compress) {
      splay(t);
    }
    node* n = nullptr;
    {
      node* par = t->parent();
      if(!par) { break; }
      else if(par->type() == Type::Rake) {
        par->push();
        splay(par);
        n = par->parent();
      }
      else if(par->type() == Type::Compress) {
        par->push();
        n = par;
      }
      else { assert(false); }
    }

    splay(n);

    
    int dir = parent_dir(n);
    if(dir == -1 || n->parent()->type() == Type::Rake) dir = 0;
    if(dir == 1) {
      n->child(dir)->reverse();
      n->child(dir)->push();
      t->reverse();
      t->push();
    }
    int n_dir = parent_dir(t);
    if(n_dir != -1) {
      node* nch = n->child(dir);
      nch->push();
      node* rake = t->parent();
      rake->push();

      rake->set_child(nch, n_dir);
      nch->set_parent(rake);
      n->set_child(t, dir);
      t->set_parent(n);
      nch->fix();
      rake->fix();
      t->fix();
      n->fix();
      splay(rake);
    }
    else {
      node* nch = n->child(dir);
      nch->push();
      n->set_rake(nch);
      nch->set_parent(n);
      n->set_child(t, dir);
      t->set_parent(n);

      nch->fix();
      t->fix();
      n->fix();
    }
    if(t->type() == Type::Edge) {
      t = n;
    }
  }
  
  return t;
}

node* expose(vertex ver) {
  return expose_raw(ver.handle());
}
```
