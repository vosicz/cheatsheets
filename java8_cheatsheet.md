
###Concatenate two lists
List<String> newList = Stream.concat(listOne.stream(), listTwo.stream()).collect(Collectors.toList());

