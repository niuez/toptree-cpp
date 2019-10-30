# Guard

Guard処理について書きます. 今まで書いたやつを編集します.

```cpp

struct node {
  ...
  bool guard;
};

int parent_dir(node* child) {
  node* par = child->parent();
  if(par) {
    if(par->guard) { return -1; }
    else if(par->child(0) == child) { return 0; }
    else if(par->child(1) == child) { return 1; }
    else { return -1; }
  }
  else { return -1; }
}

int parent_dir_guard(node* child) {
  node* par = child->parent();
  if(par) {
    if(par->child(0) == child) { return 0; }
    else if(par->child(1) == child) { return 1; }
    else { return -1; }
  }
  else { return -1; }
}

void rotate(node* t, node* x, std::size_t dir) {
  node* y = x->parent();
  int par = parent_dir_guard(x);
  t->child(dir)->push();
  x->set_child(t->child(dir), dir ^ 1);
  t->child(dir)->set_parent(x);
  t->set_child(x, dir);
  x->set_parent(t);
  t->set_parent(y);
  if(par != -1) {
    y->set_child(t, par);
  }
  else if(y && y->type() == Type::Compress) {
    y->set_rake(t);
  }
  x->fix();
  t->fix();
  if(y && !y->guard) { y->fix(); }
}

void splay(node* t) {
  assert(t->type() != Type::Edge);
  t->push();

  while(parent_dir(t) != -1) {
    node* q = t->parent();
    if(q->type() != t->type()) break;
    if(parent_dir(q) != -1 && q->parent() && q->parent()->type() == q->type()) {
      node* r = q->parent();
      if(r->parent()) r->parent()->push();
      r->push();
      q->push();
      t->push();
      int qt_dir = parent_dir(t);
      int rq_dir = parent_dir(q);
      if(rq_dir == qt_dir) {
        rotate(q, r, rq_dir ^ 1);
        rotate(t, q, qt_dir ^ 1);
      }
      else {
        rotate(t, q, qt_dir ^ 1);
        rotate(t, r, rq_dir ^ 1);
      }
    }
    else {
      if(q->parent()) q->parent()->push();
      q->push();
      t->push();
      int qt_dir = parent_dir(t);
      rotate(t, q, qt_dir ^ 1);
    }
  }
}

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
        if(par->guard && parent_dir_guard(t) != -1) { break; }
        n = par;
      }
      else { assert(false); }
    }

    splay(n);

    
    int dir = parent_dir_guard(n);
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
