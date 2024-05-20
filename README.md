# laravel-exception-handler
Laravel exception handler code snippets

```php
<?php

namespace App\Exceptions;

use CommonHelpers;
use Exception;
use Illuminate\Auth\AuthenticationException;
use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use Illuminate\Http\Request;
use Redirect;
use Session;
use Symfony\Component\HttpKernel\Exception\MethodNotAllowedHttpException;
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
use \Illuminate\Database\QueryException;
use Lang;
use App;
use Illuminate\Session\TokenMismatchException;

class Handler extends ExceptionHandler
{
    /**
     * A list of the exception types that are not reported.
     *
     * @var array
     */
    protected $dontReport = [
        //
    ];

    /**
     * A list of the inputs that are never flashed for validation exceptions.
     *
     * @var array
     */
    protected $dontFlash = [
        'password',
        'password_confirmation',
    ];

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {

        $log = array();

        $log['url'] = $request->path();
        if ($exception instanceof MethodNotAllowedHttpException) {

            return parent::render($request, $exception);

        }
        if ($exception instanceof AuthenticationException) {
            $log['Remarks'] = "Exception : Unauthorised Action.";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return redirect('/')->with('error', $log['Remarks']);
        }
        if ($exception instanceof NotFoundHttpException) {
            $log['Remarks'] = "Exception : The requested URL was not found";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.404', [], 404);
        }
        if ($exception instanceof QueryException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        if ($exception instanceof \BadMethodCallException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        if ($exception instanceof \PDOException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        if ($exception instanceof \ErrorException) {

            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);

        }
        if ($exception instanceof \InvalidArgumentException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        if ($exception instanceof \Swift_TransportException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        if ($exception instanceof \Illuminate\Session\TokenMismatchException) {
            Session::flush();
            App::setLocale('jp');
            return redirect('/')->with('error', Lang::get('errormessages.csrftokenfailed'));
        }

        if ($exception instanceof \Exception) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonHelpers::auditlog("warning", "Exception", $log, $request);
            return response()->view('errors.500', [], 500);
        }
        return parent::render($request, $exception);
    }
}

```

```php
<?php

namespace App\Exceptions;

use App\Http\Controllers\CommonController;
use Exception;
use Illuminate\Auth\Access\AuthorizationException;
use Illuminate\Auth\AuthenticationException;
use Illuminate\Database\Eloquent\ModelNotFoundException;
use Illuminate\Validation\ValidationException;
use Laravel\Lumen\Exceptions\Handler as ExceptionHandler;
use Symfony\Component\Debug\Exception\FatalThrowableError;
use Symfony\Component\HttpKernel\Exception\HttpException;
use Symfony\Component\HttpKernel\Exception\MethodNotAllowedHttpException;
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
use \Illuminate\Database\QueryException;

class Handler extends ExceptionHandler
{
    /**
     * A list of the exception types that should not be reported.
     *
     * @var array
     */
    protected $dontReport = [
        AuthorizationException::class,
        HttpException::class,
        ModelNotFoundException::class,
        ValidationException::class,
    ];

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $e
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {

        $log = array();

        $log['url'] = $request->path();

        if ($exception instanceof MethodNotAllowedHttpException) {
            $log['Remarks'] = "Error : Invalid HTTP Method. {" . $request->method() . "}";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "MethodNotAllowedHttpException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 405, 'errorMessage' => "Error : Method Not Allowed not Invalid HTTP Method"], 405);
        }
        if ($exception instanceof AuthenticationException) {
            $log['Remarks'] = "Exception : Unauthorised Action.";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "AuthenticationException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 401, 'errorMessage' => "Unauthorised Action"], 401);
        }
        if ($exception instanceof NotFoundHttpException) {
            $log['Remarks'] = "Exception : The requested URL was not found";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "NotFoundHttpException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 404, 'errorMessage' => "The requested URL was not found"], 404);
        }
        if ($exception instanceof QueryException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "QueryException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \BadMethodCallException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "BadMethodCallException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \PDOException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "PDOException", $log, $request);
            return response()->json(['errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \ErrorException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("error", "ErrorException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \InvalidArgumentException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "InvalidArgumentException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \Swift_TransportException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "Swift_TransportException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \Exception) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "Exception", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \FatalThrowableError) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "FatalThrowableError", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }

        return parent::render($request, $exception);
    }
}

```

```php
<?php

namespace App\Exceptions;

use App\Http\Controllers\CommonController;
use Exception;
use Illuminate\Auth\Access\AuthorizationException;
use Illuminate\Auth\AuthenticationException;
use Illuminate\Database\Eloquent\ModelNotFoundException;
use Illuminate\Validation\ValidationException;
use Laravel\Lumen\Exceptions\Handler as ExceptionHandler;
use Symfony\Component\Debug\Exception\FatalThrowableError;
use Symfony\Component\HttpKernel\Exception\HttpException;
use Symfony\Component\HttpKernel\Exception\MethodNotAllowedHttpException;
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
use \Illuminate\Database\QueryException;

class Handler extends ExceptionHandler
{
    /**
     * A list of the exception types that should not be reported.
     *
     * @var array
     */
    protected $dontReport = [
        AuthorizationException::class,
        HttpException::class,
        ModelNotFoundException::class,
        ValidationException::class,
    ];

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $e
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {

        $log = array();

        $log['url'] = $request->path();

        if ($exception instanceof MethodNotAllowedHttpException) {
            $log['Remarks'] = "Error : Invalid HTTP Method. {" . $request->method() . "}";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "MethodNotAllowedHttpException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 405, 'errorMessage' => "Error : Method Not Allowed not Invalid HTTP Method"], 405);
        }
        if ($exception instanceof AuthenticationException) {
            $log['Remarks'] = "Exception : Unauthorised Action.";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "AuthenticationException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 401, 'errorMessage' => "Unauthorised Action"], 401);
        }
        if ($exception instanceof NotFoundHttpException) {
            $log['Remarks'] = "Exception : The requested URL was not found";
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "NotFoundHttpException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 404, 'errorMessage' => "The requested URL was not found"], 404);
        }
        if ($exception instanceof QueryException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "QueryException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \BadMethodCallException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "BadMethodCallException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \PDOException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "PDOException", $log, $request);
            return response()->json(['errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \ErrorException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("error", "ErrorException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \InvalidArgumentException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "InvalidArgumentException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \Swift_TransportException) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "Swift_TransportException", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \Exception) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "Exception", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }
        if ($exception instanceof \FatalThrowableError) {
            $log['Remarks'] = 'Exception : ' . $exception->getMessage();
            $log['Details'] = $exception->getFile() . " " . $exception->getLine();
            $log['Trace'] = $exception->getTraceAsString();
            CommonController::auditlog("warning", "FatalThrowableError", $log, $request);
            return response()->json(['success' => false, 'errorCode' => 400, 'errorMessage' => $exception->getMessage()], 400);
        }

        return parent::render($request, $exception);
    }
}

```
