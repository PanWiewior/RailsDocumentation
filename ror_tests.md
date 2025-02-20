# Testy

## Uruchamianie testów

### Pojedynczy test

`rails test test/models/post_test.rb`

pojedynczy z pojedynczego:  
`rails test test/controllers/posts_controller_test.rb -n test_should_get_show`  
albo:  
``rails test test/controllers/posts_controller_test.rb:6``  
(6 - numer wiersza, w którym zdefiniowany jest test)

### Wszystkie testy

`rails test`

## Guide omawiający testy

<https://guides.rubyonrails.org/testing.html>

## Testy modeli

Przykład dla modelu:

```ruby
test "formats author" do
  p = Post.create(author: 'john locke', title: 'How about AJAX?')
  assert_equal 'John Locke', p.author
end
```

## Functional tests (inaczej testy kontrolerów)

Przykład:

```ruby
test "should create article" do
  assert_difference('Article.count') do
    post articles_url, params: { article: { body: 'Rails is awesome!', title: 'Hello Rails' } }
  end
 
  assert_redirected_to article_path(Article.last)
end
```

## System tests (w kursie helion nazywane integracyjnymi)

Nie są uruchamiane automatycznie przy wydawaniu polecenia rails test. Trzeba wydać polecenie:
