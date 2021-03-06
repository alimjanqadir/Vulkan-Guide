# VK_KHR_imageless_framebuffer

> Promoted to core in Vulkan 1.2

When creating a `VkFramebuffer` you normally need to pass the `VkImageView`s being used in `VkFramebufferCreateInfo::pAttachments`.

To use an imageless `VkFramebuffer`

* Make sure the implementation has support for it by querying `VkPhysicalDeviceImagelessFramebufferFeatures::imagelessFramebuffer` or `VkPhysicalDeviceVulkan12Features::imagelessFramebuffer`
* Set the `VK_FRAMEBUFFER_CREATE_IMAGELESS_BIT` in `VkFramebufferCreateInfo::flags`
* Include a `VkFramebufferAttachmentsCreateInfo` struct in the `VkFramebufferCreateInfo::pNext`
* When beginning the render pass, pass in a `VkRenderPassAttachmentBeginInfo` structure into `VkRenderPassBeginInfo::pNext` with the compatible attachments

```cpp
// Fill information about attachment
VkFramebufferAttachmentImageInfo attachments_image_info = {};
// ...

VkFramebufferAttachmentsCreateInfo attachments_create_info = {};
// ...
attachments_create_info.attachmentImageInfoCount = 1;
attachments_create_info.pAttachmentImageInfos = &attachments_image_info;

// Create FrameBuffer as imageless
VkFramebufferCreateInfo framebuffer_info = {};
framebuffer_info.pNext = &attachments_create_info;
framebuffer_info.flags |= VK_FRAMEBUFFER_CREATE_IMAGELESS_BIT;
// ...
framebffer_info.pAttachments = NULL; // pAttachments is ignored here now

vkCreateFramebuffer(device, &framebuffer_info, NULL, &framebuffer_object);

// ...

// Start recording a command buffer
VkRenderPassAttachmentBeginInfo attachment_begin_info = {};
// attachment_begin_info.pAttachments contains VkImageView objects

VkRenderPassBeginInfo begin_info = {};
begin_info.pNext = &attachment_begin_info;
// ...

vkCmdBeginRenderPass(command_buffer, &begin_info, VK_SUBPASS_CONTENTS_INLINE);
```