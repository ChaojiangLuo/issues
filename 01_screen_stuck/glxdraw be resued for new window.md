
# Environment
Uniontech OS(Deepin) V20
KDE:5.27.2
Qt:5.11.3
Mesa:19.2.6

# Test
For this issue, has no simple steps to reproduce it
We run a pressure test, something like this:
* start some apps
* input Alt + Tab to start Tabbox
* change the current window
* wait sometime and try again

# Issue
Screen get stuck, blocked at dri3_wait_for_event_locked
```
#0  0x000000fff1bf361c in __GI___poll (fds=0xfffbd42890, nfds=1, timeout=<optimized out>) at ../sysdeps/unix/sysv/linux/poll.c:41
#1  0x000000ffe7602fe0 in dri3_wait_for_event_locked (draw=0x129cc0cf8, full_sequence=0x0) at ../src/loader/loader_dri3_helper.c:563
#2  0x000000ffe7603494 in dri3_find_back (draw=0x129cc0cf8) at ../src/loader/loader_dri3_helper.c:707
#3  0x000000ffe7605d5c in dri3_get_buffer (driDrawable=0xffe0009a90, format=4099, buffer_type=loader_dri3_buffer_back, draw=0x129cc0cf8) at ../src/loader/loader_dri3_helper.c:1837
#4  0x000000ffe76064dc in loader_dri3_get_buffers (driDrawable=0xffe0009a90, format=4099, stamp=0x129cd6930, loaderPrivate=0x129cc0cf8, buffer_mask=1, buffers=0xfffbd42a90)
    at ../src/loader/loader_dri3_helper.c:2056
#5  0x000000ffe5c04b54 in dri_image_drawable_get_buffers (drawable=0x129cd6930, images=0xfffbd42a90, statts=0x129ee5a80, statts_count=2) at ../src/gallium/state_trackers/dri/dri2.c:270
#6  0x000000ffe5c04f28 in dri2_allocate_textures (ctx=0x128f59db0, drawable=0x129cd6930, statts=0x129ee5a80, statts_count=2) at ../src/gallium/state_trackers/dri/dri2.c:397
#7  0x000000ffe5c09fd8 in dri_st_framebuffer_validate (stctx=0x1290036e0, stfbi=0x129cd6930, statts=0x129ee5a80, count=2, out=0xfffbd42c38) at ../src/gallium/state_trackers/dri/dri_drawable.c:85
#8  0x000000ffe6307cb4 in st_framebuffer_validate (stfb=0x129ee55b0, st=0x1290036e0) at ../src/mesa/state_tracker/st_manager.c:222
#9  0x000000ffe6309cf4 in st_manager_validate_framebuffers (st=0x1290036e0) at ../src/mesa/state_tracker/st_manager.c:1178
#10 0x000000ffe66655b4 in st_validate_state (st=0x1290036e0, pipeline=ST_PIPELINE_CLEAR) at ../src/mesa/state_tracker/st_atom.c:202
#11 0x000000ffe666e8d8 in st_Clear (ctx=0x128fdf480, mask=50) at ../src/mesa/state_tracker/st_cb_clear.c:438
#12 0x000000ffe66409a0 in clear (no_error=false, mask=17664, ctx=0x128fdf480) at ../src/mesa/main/clear.c:221
#13 0x000000ffe66409a0 in _mesa_Clear (mask=17664) at ../src/mesa/main/clear.c:242
#14 0x000000ffec03925c in shared_dispatch_stub_203 (mask=17664) at /home/uos/mesa/mesa/build/src/mapi/shared-glapi/glapi_mapi_tmp.h:20235
#15 0x000000fff3f0f324 in QSGBatchRenderer::Renderer::renderBatches() () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#16 0x000000fff3f14a70 in QSGBatchRenderer::Renderer::render() () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5 
#17 0x000000fff3f05610 in QSGRenderer::renderScene(QSGBindable const&) () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#18 0x000000fff3f05b40 in QSGRenderer::renderScene(unsigned int) () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#19 0x000000fff3f41cec in QSGDefaultRenderContext::renderNextFrame(QSGRenderer*, unsigned int) () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#20 0x000000fff3facb90 in QQuickWindowPrivate::renderSceneGraph(QSize const&) () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#21 0x000000fff3f0f324 in QSGBatchRenderer::Renderer::renderBatches() () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
#22 0x000000fffbd43570 in  ()
#23 0x000000fff3f0f324 in QSGBatchRenderer::Renderer::renderBatches() () at /lib/loongarch64-linux-gnu/libQt5Quick.so.5
```
# Why?

## create QXcbWindow 0x2bcc5d10, xid 26537317, make current 0x2a4e95d0

```
2024-08-13 13:07:40 uos-PC kwin_x11:  create:457 ut-gfx-qtbase: QXcbWindow=0x2bcc5d10 m_window=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:376 ut-gfx-mesa: ctx=0x2843c050 create glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:407 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2c094220 draw xDrawable=26537318 refcount=1
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:419 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2c094220 read xDrawable=26537318 refcount=0
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  doneCurrent:609 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2b013dc0 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:407 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 draw xDrawable=26537317 refcount=3
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:419 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 read xDrawable=26537317 refcount=2
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  doneCurrent:609 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2b013dc0 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:407 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 draw xDrawable=26537317 refcount=3
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:419 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 read xDrawable=26537317 refcount=2
```

## done current 0x2a4e95d0

```
2024-08-13 13:07:40 uos-PC kwin_x11:  doneCurrent:609 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:40 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:40 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2b013dc0 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:40 uos-PC kwin_x11:  destroy:1920 ut-gfx-qtbase: QWindowPrivate=0x2a416ef0 delete platformWindow=0x2bcc5d20 visibilityOnDestroy=0 wasVisible=0
2024-08-13 13:07:40 uos-PC kwin_x11[1773]:  operator() 363 ut-gfx:TabBox destory window  0x28779a00  w  QQuickWindow_QML_15(0x289f1f00 active exposed, visibility=QWindow::Visibility(Hidden), flags=QFlags<Qt::WindowType>(X11BypassWindowManagerHint|FramelessWindowHint), geometry=556,432 808x216)
```

## destroy QXcbWindow 0x2bcc5d10, xid 26537317

```
2024-08-13 13:07:40 uos-PC kwin_x11:  destroy:1927 ut-gfx-qtbase: QWindowPrivate=0x2a416ef0 delete platformWindow done

2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2b013dc0 glxDrawable=26537341
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2b013dc0 find glxDrawable=26537341 pdraw=0x2e9e8730
2024-08-13 13:07:41 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2b013dc0 pdraw=0x28523a70 xDrawable=0
```

##  create another QXcbWindow 0x2e2a02b0 m_window=26537317, pdraw=0x2e27bac0

```
2024-08-13 13:07:41 uos-PC kwin_x11:  create:457 ut-gfx-qtbase: QXcbWindow=0x2e2a02b0 m_window=26537317
2024-08-13 13:07:41 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:346 ut-gfx-mesa: ctx=0x2843c050 glxDrawable=26537317
2024-08-13 13:07:41 uos-PC kwin_x11:  driFetchDrawable:363 ut-gfx-mesa: ctx=0x2843c050 find glxDrawable=26537317 pdraw=0x2e27bac0
2024-08-13 13:07:41 uos-PC kwin_x11:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x28523a70 xDrawable=0
2024-08-13 13:07:41 uos-PC kwin_x11:  driReleaseDrawables:407 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 draw xDrawable=26537317 refcount=3
2024-08-13 13:07:41 uos-PC kwin_x11:  driReleaseDrawables:419 ut-gfx-mesa: ctx=0x2843c050 pdraw=0x2e27bac0 read xDrawable=26537317 refcount=2
2024-08-13 13:07:41 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:41 uos-PC kwin_x11:  makeCurrent:550 ut-gfx-qtbase: QGLXContext=0x2a4e95d0 m_ownsContext=1 m_context=0x2843c050
2024-08-13 13:07:43 uos-PC dman:  driFetchDrawable:346 ut-gfx-mesa: ctx=0xfffeec7e5200 glxDrawable=92274716
2024-08-13 13:07:43 uos-PC dman:  driFetchDrawable:363 ut-gfx-mesa: ctx=0xfffeec7e5200 find glxDrawable=92274716 pdraw=0xfffeec2a37a0
2024-08-13 13:07:43 uos-PC dman:  driFetchDrawable:346 ut-gfx-mesa: ctx=0xfffeec7e5200 glxDrawable=92274716
2024-08-13 13:07:43 uos-PC dman:  driFetchDrawable:363 ut-gfx-mesa: ctx=0xfffeec7e5200 find glxDrawable=92274716 pdraw=0xfffeec2a37a0
2024-08-13 13:07:43 uos-PC dman:  driReleaseDrawables:401 ut-gfx-mesa: ctx=0xfffeec7e5200 pdraw=0xaaab0b7dff70 xDrawable=0
```

## QXcbWindow 0x2e2a02b0 and QXcbWindow 0x2bcc5d10 get same xid with xcb_generate_id, pdraw=0x2e27bac0 not be released when QXcbWindow 0x2bcc5d10 destoryed.
```
void QXcbWindow::create()
{
    ... ...
    m_window = xcb_generate_id(xcb_connection());
    xcb_create_window(xcb_connection(),
                      m_depth,
                      m_window,                        // window id
                      xcb_parent_id,                   // parent window id
                      rect.x(),
                      rect.y(),
                      rect.width(),
                      rect.height(),
                      0,                               // border width
                      XCB_WINDOW_CLASS_INPUT_OUTPUT,   // window class
                      m_visualId,                      // visual
                      mask,
                      values);


void QXcbWindow::destroy()
{
    ... ...
    if (m_window) {
        ... ...
        connection()->removeWindowEventListener(m_window);
        xcb_destroy_window(xcb_connection(), m_window);
        m_window = 0;
    }
```

# For drawable

## setup_present_event at first_init

```
static int
dri3_update_drawable(struct loader_dri3_drawable *draw)
{
   mtx_lock(&draw->mtx);
   if (draw->first_init) {
      xcb_get_geometry_cookie_t                 geom_cookie;
      xcb_get_geometry_reply_t                  *geom_reply;
      xcb_window_t                               root_win;

      draw->first_init = false;
      draw->reused = false;

      if (!dri3_setup_present_event(draw)) {
         mtx_unlock(&draw->mtx);
         return false;
      }
```

## unregister_for_special_event at drawable_fini

```
void
loader_dri3_drawable_fini(struct loader_dri3_drawable *draw)
{
   int i;

   syslog(LOG_WARNING, "%s:%d ut-gfx-mesa: draw=%p %d\n", __FUNCTION__, __LINE__, draw, draw->drawable);

   draw->ext->core->destroyDrawable(draw->dri_drawable);

   for (i = 0; i < ARRAY_SIZE(draw->buffers); i++)
      dri3_free_render_buffer(draw, i);

   if (draw->special_event) {
      syslog(LOG_WARNING, "%s:%d ut-gfx-mesa: draw=%p %d special_event select input none\n", __FUNCTION__, __LINE__, draw, draw->drawable);
      xcb_void_cookie_t cookie =
         xcb_present_select_input_checked(draw->conn, draw->eid, draw->drawable,
                                          XCB_PRESENT_EVENT_MASK_NO_EVENT);

      xcb_discard_reply(draw->conn, cookie.sequence);
      xcb_unregister_for_special_event(draw->conn, draw->special_event);
   }
```


The QXcbWindow has been released and created, the xid is same for the tow window. And drawable not been recreated, so no special event been setup with server side window. The server side Xorg also recreate the window, just the xid is the same.

The gl drawable can not be released by done curren:glXMakeCurrent(m_display, 0, 0) right now and it'will released at next glXMakeCurrent(m_display, newWindowId, context). If the xid is the same as the last time. Glx(dri3) doesn't know the window(xid) has been changed and doesn't select new input with Xorg. So the dri will be blocked to wait events at xcb_wait_for_special_event.

