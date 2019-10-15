# `erlfmt_parse`

## Differences to `erl_parse`

In `erlfmt_parse` the following AST nodes have different definitions:

* The record name is always represented as a full `{atom, Anno, Name}` node,
  instead of the raw atom `Name`. This affects the following AST nodes:
  * `{record, Anno, Name, Fields}`
  * `{record, Anno, Expr, Name, Updates}`
  * `{record_index, Anno, Name, Field}`
  * `{record_field, Anno, Expr, Name, Field}`
  * `{type, Anno, record, [Name | Fields]}`
  * `{attribute, Anno, record, {Name, Fields}}`

* The attribute values are not "normalized" - they are represented in the
  abstract term format instead of as concrete terms.

* A new attribute with a special value is recognised:
  `{attribute, Anno, define, {Type, Name, Args, Body}}`, where
  * `Type` is `expr` or `clause`,
  * `Name` is either an `atom` node or a `var` node,
  * `Args` is either a list of vars or an atom `none`,
  * `Body` is:
    * for `expr`, it is a list of lists of expressions (equivalent to guards in clauses),
      an atom `empty` or a `{record_name, Anno, Name}` node,
    * for `clause`, it is a `clause` node as used in functions.

* The `clause` node as used in functions or funs has a different AST representation:
  `{clause, Anno, Name, Args, Guards, Body}`, where the newly added `Name` field
  is an `atom` node, a `var` node or an atom `'fun'` for anonymous funs.

* The `function` node has a different AST representation:
  `{function, Anno, Clauses}`, where `Clauses` is a list of `clause` nodes.
  Additionally it is less strict - it does not enforce all clauses have
  the same name and arity.

* The `fun` node has a different AST representation:
  `{'fun', Anno, Value}`, where `Value` is one of:
  * `{function, Name, Arity}`, where `Name` and `Arity` are an `atom` node
    and an `integer` node respectively,
  * `{function, Module, Name, Arity}`, where `Module`, `Name`, and `Arity`
    are `atom`, `atom`, and `integer` nodes respectively or a `var` node.
  * `{clauses, Clauses}`, where `Clauses` is a list of `clause` nodes.
    Additionally it is less strict - the clauses aren't checked for the same
    name or arity.

* The `named_fun` node is not used.