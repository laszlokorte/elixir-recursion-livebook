<img src="icon.svg" width="100" height="100" />

# Introduction Recursion Schemes

[This Livebook](./intro.livemd) is an introduction to the concept of [_recursion schemes_](https://github.com/passy/awesome-recursion-schemes) in functional programming via Elixir.

You might enjoy it if you would like to understand the code snippet below.
You will learn what are _Catamorphisms_, _Anamorphisms_ and _Hylomorphism_.
It will let you see an interesting connection structural connection between fibonacci numbers, `n!`, summing sequences, Mergesort, Binary-Search and more.

The Livebook based on this [javascript based introduction](https://static.laszlokorte.de/recursion-schemes/).
Due to support structural pattern matching the Elixir code might be much more concise and more reable than the JavaScript code.

```elixir
search_tree =
  [4, 16, 8, 3, 42, 6, 7, 67, 108]
  # Merge-Sort is an hylomorphism
  |> Hylomorphism.hylo(
    &LeafTreeStructure.recursion/2,
    # splitting the list into a tree
    &LeafTreeStructure.CoAlgebras.from_list/1,
    # mergng the tree back into a list
    &LeafTreeStructure.Algebras.merge/1
  )
  |> Anamorphism.ana(
    # splitting the sorted list into a balacned search-tree
    &SearchTreeStructure.recursion/2,
    &SearchTreeStructure.CoAlgebras.from_list/1
  )

for needle <- [12, 42] do
  search_tree
  |> Hylomorphism.hylo(
    # binary search is also an hylomorphism
    &LinkedListStructure.recursion/2,
    # build search path down the tree as list of boolean (hit/miss) values
    BinarySearchSequence.CoAlgebras.binary_search_ana(needle),
    # collapse the search sequency into a single resulting value (true ~ found, false ~ not found)
    &LinkedListStructure.Algebras.any?/1
  )
  |> IO.inspect(label: "Binary-Search for number #{needle}", charlists: :as_list)

  search_tree
  |> Anamorphism.ana(
    # binary search can also be an anamorphism
    &LinkedListStructure.recursion/2,
    # if we ae not interested in a single true/false result but in the
    # full path down the tree as list of nodes
    BinarySearchSequence.CoAlgebras.binary_search_sequence_ana(needle)
  )
  |> IO.inspect(label: "Binary-Search for number #{needle}", charlists: :as_list)

  search_tree
  |> Anamorphism.ana(
    &LinkedListStructure.recursion/2,
    # search for number in tree, output :left or :right turns on the way down
    BinarySearchSequence.CoAlgebras.binary_search_direction_ana(needle)
  )
  |> IO.inspect(label: "Binary-Search for number #{needle}", charlists: :as_list)
end

# => Binary-Search for number 12: false
# => Binary-Search for number 12: [8, 67, 42, 16]
# => Binary-Search for number 12: [:right, :left, :left, :miss]
# => Binary-Search for number 42: true
# => Binary-Search for number 42: [8, 67, 42]
# => Binary-Search for number 42: [:right, :left, :found]
```
