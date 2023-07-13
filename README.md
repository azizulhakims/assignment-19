laravel new blog

php artisan make:migration create_posts_table

php artisan migrate

php artisan make:model Post

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $table = 'posts';

    protected $fillable = ['title', 'content', 'published_at'];
}

php artisan make:controller PostController

<?php

namespace App\Http\Controllers;

use App\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::all();

        return view('posts.index', ['posts' => $posts]);
    }
}

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PostController;

Route::get('/posts', [PostController::class, 'index']);


<h1>Blog Post List</h1>

<ul>
    @foreach($posts as $post)
        <li>
            <h2>{{ $post->title }}</h2>
            <p>{{ $post->content }}</p>
            <p>Published at: {{ $post->published_at }}</p>
        </li>
    @endforeach
</ul>

php artisan serve


=====================


Route::get('/posts/{post}', [PostController::class, 'show']);

<?php

namespace App\Http\Controllers;

use App\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $posts = Post::all();

        return view('posts.index', ['posts' => $posts]);
    }

    public function show(Post $post)
    {
        return view('posts.show', ['post' => $post]);
    }
}

<h1>{{ $post->title }}</h1>
<p>{{ $post->content }}</p>
<p>Published at: {{ $post->published_at }}</p>


<ul>
    @foreach($posts as $post)
        <li>
            <h2><a href="{{ route('posts.show', $post) }}">{{ $post->title }}</a></h2>
            <p>{{ $post->content }}</p>
            <p>Published at: {{ $post->published_at }}</p>
        </li>
    @endforeach
</ul>

php artisan serve


composer require stevebauman/commentable

php artisan vendor:publish --provider="Stevebauman\Commentable\CommentableServiceProvider" --tag="migrations"


php artisan migrate




=================
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Stevebauman\Commentable\Traits\HasComments;
use Stevebauman\Commentable\Contracts\Commentable;

class Post extends Model implements Commentable
{
    use HasComments;

    // ...
}


<h1>{{ $post->title }}</h1>
<p>{{ $post->content }}</p>
<p>Published at: {{ $post->published_at }}</p>

<!-- Comment Form -->
<form action="{{ route('comments.store') }}" method="POST">
    @csrf
    <input type="hidden" name="commentable_type" value="App\Post">
    <input type="hidden" name="commentable_id" value="{{ $post->id }}">
    <textarea name="content" cols="30" rows="5"></textarea>
    <button type="submit">Submit</button>
</form>

<!-- Display Comments -->
<h2>Comments</h2>
@foreach($post->comments as $comment)
    <p>{{ $comment->content }}</p>
@endforeach


<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Stevebauman\Commentable\Models\Comment;

class CommentController extends Controller
{
    public function store(Request $request)
    {
        Comment::create($request->all());

        return redirect()->back();
    }
}\



Route::post('/comments', [CommentController::class, 'store'])->name('comments.store');



php artisan serve
