# Select

toptree上の二分探索であるselect操作を書きます. 今すぐ欲しいとかじゃない場合は後回しで大丈夫です.

```cpp
node* select_rake(node* rake, cluster& right, cluster::V& rv0, cluster::V& rv1) {
  rake->push();
  while(rake->type() == Type::Rake) {
    node* l = rake->child(0);
    node* r = rake->child(1);
    l->push();
    r->push();

    cluster rf = cluster::rake(r->fold(), right, r->endpoint(0).value(), rv0, r->endpoint(1).value());
    cluster::V r0 = r->endpoint(0).value();

    std::size_t dir = cluster::select(l->fold(), rf, l->endpoint(0).value(), r0, l->endpoint(1).value());
    r = rake->child(1 - dir);
    rake = rake->child(dir);


    right = cluster::rake(r->fold(), right, r->endpoint(0).value(), rv0, r->endpoint(1).value());
    rv0 = r->endpoint(0).value();
    rv1 = r->endpoint(1).value();

    rake->push();
  }
  return rake;
}

std::pair<vertex, vertex> select(vertex v) {
  node* n = expose(v);
  cluster lf = cluster::identity();
  cluster::V l0, l1;
  bool luse = false;
  cluster rf = cluster::identity();
  cluster::V r0, r1;
  bool ruse = false;

  n->push();
  while(n->type() == Type::Compress) {
    node* a = n->child(0);
    node* b = n->child(1);
    node* r = n->rake();
    a->push();
    b->push();
    if(r) { r->push(); }

    cluster af = a->fold();
    cluster::V a0 = a->endpoint(0).value();
    cluster::V a1 = a->endpoint(1).value();
    if(luse) {
      af = cluster::compress(lf, af, l0, a1, l1);
      a0 = l0;
      a1 = a1;
    }
    cluster bf = b->fold();
    cluster::V b0 = b->endpoint(0).value();
    cluster::V b1 = b->endpoint(1).value();
    if(ruse) {
      bf = cluster::compress(bf, rf, b0, r1, b1);
      b0 = b0;
      b1 = r1;
    }
    cluster arf = af;
    if(r) {
      arf = cluster::rake(af, r->fold(), a0, r->endpoint(0).value(), a1);
    }

    std::size_t dir = cluster::select(arf, bf, a0, b1, a1);
    
    if(dir == 0) {
      if(r) {
        cluster rbf = cluster::reverse(bf);
        cluster::V rb0 = b1;

        cluster rrf = cluster::rake(r->fold(), rbf, r->endpoint(0).value(), rb0, r->endpoint(1).value());
        cluster::V rr0 = r->endpoint(0).value();
        cluster::V rr1 = r->endpoint(1).value();

        dir = cluster::select(af, rrf, a0, rr0, a1);
        if(dir == 0) {
          rf = cluster::reverse(rrf);
          r0 = rr1;
          r1 = rr0;
          ruse = true;
          n = n->child(0);
        }
        else {
          luse = false;
          rf = cluster::rake(af, rbf, a0, rb0, a1);
          r0 = a0;
          r1 = a1;
          ruse = true;
          n = select_rake(r, rf, r0, r1);
          rf = cluster::reverse(rf);
          std::swap(r0, r1);
        }
      }
      else {
        rf = bf;
        r0 = b0;
        r1 = b1;
        ruse = true;
        n = n->child(0);
      }
    }
    else {
      lf = arf;
      l0 = a0;
      l1 = a1;
      luse = true;
      n = n->child(1);
    }

    n->push();
  }
  return { n->endpoint(0), n->endpoint(1) };
}
```
