Constexpr If
------------


Actually, how _would_ someone write a custom `get<>()` function for their class?
(see [Structured Bindings](structured_bindings.md) for why you might want to do that)
Since each `get<0>`, `get<1>`, etc returns a different member, which are possibly different types...
(oh no, template metaprogramming...)

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td valign="top">
<pre lang="cpp">
class Foo {
  int myInt;
  string myString;
public:
  int const & refInt() { return myInt; }
  string const & refString() { return myString; }
};



// Hey compiler, keep an eye out for this template:
template&lt;int N&gt; void get(Foo const &amp; foo)
{
  static_assert(false, "Foo only has 2 members");
}
// here's some specializations (the real stuff)
template&lt;&gt; int const & get&lt;0&gt;(Foo const &amp; foo)
{
  return foo.refInt();
}
template&lt;&gt; string const & get&lt;1&gt;(Foo const &amp; foo)
{
  return foo.refString();
}
</pre>
</td>
<td valign="top">
<pre lang="cpp">
class Foo {
  int myInt;
  string myString;
public:
  int const & refInt() { return myInt; }
  string const & refString() { return myString; }
};



template&lt;int N&gt; auto get(Foo const &amp; foo)
{
  if constexpr (N == 0)
     return foo.refInt();
  else if constexpr (N == 1)
     return foo.refString();
  else
     static_assert(false, "Foo only has 2 members");
}
</pre>
</td>
</tr>
</table>