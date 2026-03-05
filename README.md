# Recursion Schemes

This livebook is an introduction to the function programming concept of _recursion schemes_ via elixir.

You might enjoy it if you would like to understand this code snippet.
You will learn what are catamorphisms, anamorphisms and hylomorphism and will let you see the connection between fibonacci numbers, faculty, summing integers, merge sort, binary search.

```elixir
search_tree =
  [4, 16, 8, 3, 42, 6, 7, 67, 108]
  |> Hylomorphism.hylo(
    &LeafTreeStructure.recursion/2,
    &LeafTreeStructure.CoAlgebras.from_list/1,
    &LeafTreeStructure.Algebras.merge/1
  )
  |> Anamorphism.ana(
    &SearchTreeStructure.recursion/2,
    &SearchTreeStructure.CoAlgebras.from_list/1
  )

for needle <- [12, 42] do
  search_tree
  |> Hylomorphism.hylo(
    &LinkedListStructure.recursion/2,
    BinarySearchSequence.CoAlgebras.binary_search_ana(needle),
    &LinkedListStructure.Algebras.any?/1
  )
  |> IO.inspect(label: "Binary-Search for number #{needle}", charlists: :as_list)

  search_tree
  |> Anamorphism.ana(
    &LinkedListStructure.recursion/2,
    BinarySearchSequence.CoAlgebras.binary_search_sequence_ana(needle)
  )
  |> IO.inspect(label: "Binary-Search for number #{needle}", charlists: :as_list)

  search_tree
  |> Anamorphism.ana(
    &LinkedListStructure.recursion/2,
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
